
Viktige tema:
- **Feilkilder innen vitenskaplige beregninger:**  
- Hvordan bruke interpolasjon for å komme fram til nye kvadraturer 
- Typisk oppgave: Får oppgitt noen noder, og noen funksjoner, så konstuer en kvadraturregel som har minst "denne" error. Så evaluere funksjonen. Kan både komme i papirform eller i kode.
- **Degree of exactness, når er noe eksakt.** 
- **Composit Quadrature rules**
- **Convergens order, kommer oppgave om dette. Snakket noe om log log error?** 
- **Ting som dukker opp flere ganger, større sannsynlighet for kommer på eksamen.** 
- **Implementering av kvadraturregler, de ligger et par oppgaver rundt dette i manuskriptet.** 
- Mulig oppgave: Gitt et feilestimat for en kvadraturregel, finn feilestimatet for den sammensatte kvadraturregelen

- **Må kunne løse lineære difflikninger, system av difflikninger og høyere ordens difflikninger. Høyere ordens difflikninger gjør vi om til system av lineære** 
- **Typisk oppgave: Får oppgitt en type runge kutta metode, implimenter i python** 
- **Convergence of one step methods theorem**
- **Typisk oppgave: Oversette kode og butcher tables, begge veier** 
- **Addaptiv løser kommer ikke** 
- **Forstå eksempler om stiffe ODE problemer** 
- **Stabilitetsfunksjoner** 

- Indreprodukt av funksjoner, og grunnleggende komplekse tall 
- **Typisk oppgave: Får oppgitt noen punkt, regn ut den diskrete fouier transformasjonen. Eksempel på slik oppgave i manuskriptet** 
- **Typisk oppgave: Får oppgitt noen punkt, regn ut fouier transformasjon av den, og så iterpolere den.** 
- Kommer noe om dette: Forstå hvordan differensialoperatorer oversettes til muliplikasjon på fourier siden?

Nyttige funksjoner i python:
```
numpy:
- np.linspace(start, stop, number of points)
- arr.astype('int'): Change the datatype of the elements of an array
- np.log(): Bedre enn å bruke math.log! 
- np.insert(EOCs, 0, np.inf): Sett inn verdi på en spesifikk plass i en array
- np.meshgrid(x,y, sparse=True): gir oss 2 arrays, størrelse x og y. Brukes som punktene som skal evalueres når vi jobber med FT. 
- np.ftt.fft(datapoints): Get the Ck coefficients in a DFT

Pandas:
table = pd.DataFrame({"Errors": errors_arr, "EOC": EOCs})
display(table)

Imaginary unit:
- Python use j as standard imaginary unit: z = 2 + 3j.  
```

Interpolation in python:
```
- when choosing x_space, make it go from xdata[start] to xdata[end] 
```


Note to self:
```
- Du glemmer ofte å skrive return i funksjonene dine.
- Når vi definerer funksjoner med numeriske metoder, der vi har et endelig set x verdier. Definer funksjonen som en np array, som evalueres funksjonen i hver av x verdiene. 
- Hvis vi skal vise en table, alltid lag en dict, og sett den in i en dataframe. Bruk display() for å vise 
- Hvis metoder gir dårlig resultat (med vilje), kan det være noe med funksjonen vi estimerer som er feil. Eksempel, ikke oppfylte anntakelser on smoothness, deriverte, divergering etc. 
- Ofte når vi skal gjøre kvadraturer, lager vi m+1 punkter isteden for m, som oppgaven spør om: x = np.linspace(a,b,m+1). 
- Når vi gjør fourier trans i python, som regel excluderer vi endpoints, gjøres med np.linspace(...,endpoint=false)
- FT: noen ganger regner vi ut Ck som en array og andre ganger finner vi formelen for ck? 
- Intergraler med abs tegn -> Splitt dem i 2 deler, en negative, en positiv. 
- Spørsmål om hvorfor en func er bedre enn en annen: smoother, den ene oppfyller ikke underliggende betingelser, ..
- DFT i python, når inkluderer vi (1/N) leddet? -> np.fft.fft() normaliserer ikke automatisk, dette må gjøres selv! Har hatt noen rare interaction med dette. Hvis ting er rart, prøv å bytt normalisering variant. I tillegg, noen formler er rare og vi må starte å telle fra index 1 isteden for 0. På eksamen, må bare prøve litt forskjellig hvis ting ikke funker 
- Når det kommer til oppgaver om å sette opp system av difflikninger. Orden av den orginal diff.likningen er lik antall variabler vi trenger. Egentlig ganske rett fram, bare lag nye variables og skriv likningen på nytt. 
```

