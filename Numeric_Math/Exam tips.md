
Viktige tema:
- Feilkilder innen vitenskaplige beregninger:  
- Hvordan bruke interpolasjon for å komme fram til nye kvadraturer 
- Typisk oppgave: Får oppgitt noen noder, og noen funksjoner, så konstuer en kvadraturregel som har minst "denne" error. Så evaluere funksjonen. Kan både komme i papirform eller i kode.
- Degree of exactness, når er noe eksakt. 
- Composit Quadrature rules
- Convergens order, kommer oppgave om dette. Snakket noe om log log error? 
- Ting som dukker opp flere ganger, større sannsynlighet for kommer på eksamen. 
- Implementering av kvadraturregler, de ligger et par oppgaver rundt dette i manuskriptet. 
- Mulig oppgave: Gitt et feilestimat for en kvadraturregel, finn feilestimatet for den sammensatte kvadraturregelen

- Må kunne løse lineære difflikninger, system av difflikninger og høyere ordens difflikninger. Høyere ordens difflikninger gjør vi om til system av lineære 
- Typisk oppgave: Får oppgitt en type runge kutta metode, implimenter i python 
- Convergence of one step methods theorem
- Typisk oppgave: Oversette kode og butcher tables, begge veier 
- Addaptiv løser kommer ikke 
- Forstå eksempler om stiffe ODE problemer 
- Stabilitetsfunksjoner 

- Indreprodukt av funksjoner, og grunnleggende komplekse tall 
- Typisk oppgave: Får oppgitt noen punkt, regn ut den diskrete fouier transformasjonen. Eksempel på slik oppgave i manuskriptet 
- Typisk oppgave: Får oppgitt noen punkt, regn ut fouier transformasjon av den, og så iterpolere den. 
- Kommer noe om dette: Forstå hvordan differensialoperatorer oversettes til muliplikasjon på fourier siden?

Nyttige funksjoner i python:
```
numpy:
np.linspace(start, stop, number of points)
arr.astype('int'): Change the datatype of the elements of an array




```

Interpolation in python:
```
- when choosing x_space, make it go from xdata[start] to xdata[end] 
```
