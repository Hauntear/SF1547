
# TOR Uppgifter Övning 1 (2.1, 2.2, 2.8, 2.9)

## Uppgift 2.1

Givet ett startintervall $[a, b]$ sådant att $f(a)f(b) < 0$ (dvs en rot r sådant att $f(r)=0$ ligger i intervallet $[a, b]$ då $f(x)$ byter tecken) så uppskattar intervallhalveringsmetoden (bisection method) roten $r$ genom följande algoritm (hämtad från Sauer):

```
while (b-a)/2 > TOL
	c = (a+b)/2
	if f(c) = 0, stop, end
	if f(a)f(c) < 0
		b = c
	else
		a = c
	end
end
r = (a+b)/2
```

där $TOL$ är en feltollerans.

I vårt fall får vi veta att en rot ligger i intervallet $[a, b] = [1, 2]$ för ekvationen $x^5 = x + 1$. Vi låter då $f(x) = x^5 - x - 1$. Utifrån detta vill vi hitta $r$ sådant att $f(r)=0$. Vi approximerar $r$ med intervallhalveringsmetoden med $TOL = 10^{-6}$ och räknar hur många iterationer det tar: 

```matlab
a = 1; b = 2; TOL = 1e-6;

f=@(x) x^5 - x - 1;

n = 0;

while (b-a)/2 > TOL
    n=n+1;
    c = (a+b)/2
    if f(c) == 0
        break
    elseif f(a)*f(c) < 0
        b = c;
    else
        a = c;
    end
end
r = (a+b)/2
n
f(r) % Vi kollar om r är en rot till f
```

Vi får $r=1.1673$ och antalet iterationer $n = 19$.

---

## Uppgift 2.2

Vi får en ekvation $cos(x) = x^2$ som har en rot nära $0.825$. I fixpunktmetoden löser vi först ut x ur ekvationen. Vi får då x = $\sqrt{cos(x)}$ (positiv rot då roten ligger nära $0.825$). Vi väljer sedan en första gissning $x_0$, i detta fall $x_0=0.825$. Den allmänna formeln för nästa iteration av x är då $x_{n+1} = \sqrt{cos(x_n)}$.

Matlab kod för att hitta roten med $TOL = 10^{-10}$:

```matlab
g=@(x) sqrt(cos(x));

x0 = inf;
x1 = 0.825

TOL = 1e-10;

while abs(x1-x0)>TOL
    
    x0 = x1;
    x1 = g(x1)
    
end

cos(x1) - x1^2 % Vi testar om resultatet är en rot
```

---

## Uppgift 2.8

Vi får att en iterativ metod uppskattas ha felen $e_1 = 0.11345$, $e_2 = 1.48995\cdot10^{-2}$, $e_3 = 2.48929 \cdot 10^{−4}$ och $e_4 = 6.416252 \cdot 10^{−8}$ under de första 4 iterationerna. Vi vill undersöka vilken konvergensordning metoden förmodligen har.

Minns att för en given iterativ metod $\frac{|e_{n+1}|}{|e_{n}|^p} \rArr S$ när $n \rArr \inf$, där $p$ är konvergensordningen av metoden. Alltså vill vi hitta $p$ sådant att $\frac{|e_{2}|}{|e_{1}|^p} \approx \frac{|e_{3}|}{|e_{2}|^p} \approx \frac{|e_{4}|}{|e_{3}|^p}$. 

Det finns en formel som kan approximera detta (härledningen finns i Ant-Konvergensordning.pdf i Canvas filerna):
$$
p_n = \frac{\log{|e_{n+1}|} - \log{|e_{n}|}}{\log{|e_{n}|} - \log{|e_{n-1}|}}
$$

Vi stoppar in respektive fel-trippletter (1, 2, 3) och (2, 3, 4) i formeln och får då resultaten $p_2 = 2.0157 \approx  p_3 = 2.0195$. Resultatet visar på att metoden förmodligen är en metod av konvergensordning 2.

Alternativ tillvägagångssätt för att hitta konvergensordning av en iterativ metod är att plotta $\frac{|e_{n+1}|}{|e_{n}|^p}$ för alla iterationer $n$ på en loglog-plot och titta på lutningen av kurvan.

---

## Uppgift 2.9

(a)

Vi sätter
$$
f(\vec{x}) = 
\left(\begin{array}{cc}
e^{x_0+x_1} + x_1 - 1.1\\
e^{x_0-x_1} - x_0 - 2x_1 - 0.94
\end{array}\right)
$$
där $\vec{x} = (x_0, x_1) = (x, y)$.

För Newtons i flera variabeler behöver vi även beräkna Jakobianen $J(\vec{x})$ till $f(\vec{x})$:

$$
J(\vec{x}) = 
\left(\begin{array}{cc}
e^{x_0 + x_1} & e^{x_0 + x_1} + 1\\
e^{x_0-x_1} - 1 & -e^{x_0-x_1} -2
\end{array}\right)
$$

Iteration $n+1$ av Newton i flera variabeler ser ut som sådant:
$$
\vec{h_n} = J(\vec{x_n})^{-1}f(\vec{x_n})
$$
$$
\vec{x_{n+1}} = \vec{x_n} - \vec{h_n}
$$
En iteration av Newton i flera variabeler med startgissning $\vec{x_0} = (0,0)$ ger $\vec{x_1} = (0.06,0.02)$. Matlab kod:

```matlab
f=@(x) [exp(x(1)+x(2)) + x(2) - 1.1;
    exp(x(1)-x(2)) - x(1) - 2*x(2) - 0.94];

J=@(x) [exp(x(1)+x(2)), exp(x(1)+x(2)) + 1;
    exp(x(1)-x(2)) - 1, -exp(x(1)-x(2)) - 2];

x = [0; 0];

h = J(x)\f(x);

x = x - h
```

(b)

Newtons metod har konvergensordning 2. Detta innebär att $|e_{n+1}| = S|e_{n}|^2$. Metoden som uppfyller detta kriteritum närmast är metod 2.

(c)

Minns $\frac{|e_{n+1}|}{|e_{n}|^p} \approx S$. Vi undersöker värdet $S$ för de olika felen med $p = 1$. Alla fel ger värden nära $0.39$, vilket tyder på att metoden har linjär konvergens. Vi försöker nu få fram den bästa uppskattningen av $S$, vilket vi får genom att använda de två sista iterationernas fel: $\frac{|e_{5}|}{|e_{4}|} \approx 0.3922$. 

Med detta kan vi ställa upp ekvationen $e_5 \cdot S^k = 0.9\cdot10^{-6}$ där $k$ är antalet ytterligare iterationer. Lösning av denna ekvation ger $k=9$ (avrundat uppåt); det krävs ungefär 9 fler iterationer.