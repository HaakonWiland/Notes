
**Exercises:**

**oppg 1 V22: Lagrange interpolation**
- Husk formelen, bare plotte inn for å finne alle l.
- Finn polynomet med formelen, du trenger y verdiene og l verdiene. 
- Når vi har funnet polynomen kan vi sjekke om det er riktig, med å sette inn de ulike x verdiene og se om vi får ut y verdiene. 

**oppg 3 S22: Lagrange interpolation, y verdier ikke oppgitt**
a)
- Får oppgitt funksjon, og x verdier 
- Sett inn x verdiene, slik at vi har en tabell med x og y verdier. 
TIPS: Når vi har y verdier som er 0, så trenger vi ikke regne ut den l verdien. Siden dette leddet blir 0 i polynomet uansett. 
- Da har vi igjen samme oppgave som tidligere. Regn ut l verdiene, og konstruer polynomet. 
NOTE: Disse oppgavene handler bare om å huske formel, og ikke gjøre fortegnsfeil. 

b) **Hva er feilen til polynomet? (f vs p)**
- Formel for dette: 
`|f(x) - P(x)| = |f^3(z(x)) * w(x) / 3! |`
- Vil finne en begrensning av rhs 
- w(x) har en egen formel for å finne grense: h er steglengden mellom x'ene, n er graden av polynomet? 
- w(x) har også en generel formel:
`w(x)=(x-x0)(x-x1)(x-x2)...`
- Kan begrense w(x) direkte, men kan også bruke estimatet hvis ikke annet er spesifisert. 

c) **Hvorfor bruke chev metode for å velge x verdier?**
- Får flere punkter på ytterpunktene av domenet, der feilene er antatt å være størst 

d) **Hvordan finne chev nodene:**
- Det er en formel for dette 
- Kan også tegne opp halvsirkelen, og dele inn i n forskjellige noder. Del opp i 90 grader, så halver denne til 45 grader, så halver hver av disse osv. 


**oppg 2 V22:** Bruke kvadratur for å løse integral 
- Denne oppgaven bruker midtpunkt metoden(hvis vi benytter n=1)
- Kan også bruke n=2, basically dele opp intervallet i 2, og benytte midtpunktmetoden for de 2 del-intervallene. -> Dette er mer nøyaktig, enn å vanlig midtintervall. 
- Har egen formel for å beregne hvor god vårt estimat er, må sjekke opp denne formel. Litt rot når har gjorde det på tavla.

**oppg 1 s22:** Simpsons metode til å regne ut integral.
- Får oppgitt at vi skal bruke 2 del-intervaller 
- Må kunne formelen for simpsons metode, så anvende denne på begge del-intervallene (Bare Q1 + Q2).
- Så skal vi finne feil, her finnes det også formel. Det finne 2 metoder. Ikke overkompliser dette tror jeg. 
- Q: Kunne vi regnet ut det faktiske integralet analytisk, og bare sammenliknet? 