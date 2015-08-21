\newcommand\fun{\textsf{fun}~}
\newcommand\fix{\textsf{fix}~}
\newcommand\ifZ{\textsf{ifZ}~}
\newcommand\Let{\textsf{let}~}
\newcommand\In{\textsf{in}~}
\newcommand\Then{~\textsf{then}~} % Uso mayúsculas para then y else porque en
\newcommand\Else{~\textsf{else}~} % minúsculas son palabras claves de LaTeX

# Programming Language for Computable Functions (PCF)

Existen 2 construcciones de base:

**Construcción explícita de función (sin nombre):** $\fun x.t$

**Aplicación de una función a un argumento:** $(\fun x.x)\ t$

Hasta acá es lo mismo que teníamos en lambda cálculo. Se agregan:

- Una constante por cada número natural.
- Operaciones: `+`, `-`, `*`, `/`. Donde 
    * $n - m = 0 ~ \textsf{si} ~ m > n$
    * `/` es la división euclidia (entera)
    * la división por 0 da error
- Test a cero: $\ifZ t \Then u \Else v$

## Ejemplos

\begin{align*}
    \textsf{Twice} = ~
    & \fun f.\fun x.f (f x)\\
    \textsf{FuncionEnXY} = ~
    & \fun x.\fun y.x*x + y\\
    \textsf{Fact} = ~
    & \fix f.\fun n.\ifZ n \Then 1 \Else n * (f (n -1))\\
\end{align*}

\begin{align*}
  \textsl{Fact}\ 2
  & = (\fix f.\fun n.\ifZ n \Then 1 \Else n*(f\ (n-1)))~2\\
  & \to (\fun n.\ifZ n\Then 1\Else n*(\textsl{Fact}\ (n-1)))~2\\
  & \to \ifZ 2\Then 1\Else 2*(\textsl{Fact}\ (2-1))\\
  & \to \ifZ 2\Then 1\Else 2*(\textsl{Fact}\ 1)\\
  & \to 2*(\textsl{Fact}\ 1)\\
  & \to 2*((\fix f.\fun n.\ifZ n \Then 1 \Else n*(f\ (n-1)))\ 1)\\
  & \to 2*((\fun n.\ifZ n \Then 1 \Else n*(\textsl{Fact}\ (n-1)))\ 1)\\
  & \to 2*(\ifZ 1 \Then 1 \Else 1*(\textsl{Fact}\ (1-1)))\\
  & \to 2*(\ifZ 1 \Then 1 \Else 1*(\textsl{Fact}\ 0))\\
  & \to 2*(1*(\textsl{Fact}\ 0))\\
  & \to 2*(1*((\fix f.\fun n.\ifZ n \Then 1 \Else n*(f\ (n-1)))\ 0))\\
  & \to 2*(1*((\fun n.\ifZ n \Then 1 \Else n*(\textsl{Fact}\ (n-1)))\ 0))\\
  & \to 2*(1*(\ifZ 0 \Then 1 \Else 0*(\textsl{Fact}\ (0-1))))\\
  & \to 2*(1*1)\\
  & \to 2*1\\
  & \to\fbox{2}\\
\end{align*}

Donde $\fix f$ significa **punto fijo** de $f$

## Gramática

\begin{align*}
    \textsl{t}
    & = x \\
    & | \fun x.t \\
    & | t t \\
    & | n \in \mathbb{Z} \\
    & | t + t | t - t | t * t | t / t \\
    & | \ifZ t \Then t \Else t \\
    & | \fix x.t \\
    & | \Let x = t ~ \In t
\end{align*}

## Ejercicios

1. Escribir un programa en PCF que tome dos enteros `n` y `p` y calcule $n^{p}$. Calcular $3^{1}$ y $3^{2}$.
$$ n^{p} = \fix f.\fun n.\fun p.\ifZ p \Then 1 \Else n * (f n (p - 1)) $$

\begin{align*}
    n^{p}\ 3\ 1
    & = (\fix f.G)\ 3\ 1 \\
    & \to (\fun n.\fun p.\ifZ p \Then 1 \Else n * (\fix f. G\ n (p - 1)))\ 3\ 1 \\
    & \to (\fun p.\ifZ p \Then 1 \Else 3 * (\fix f. G\ 3 (p - 1)))\ 1 \\
    & \to \ifZ 1 \Then 1 \Else 3 * (\fix f. G\ 3 (1 - 1)) \\
    & \to 3 * (\fix f. G\ 3\ 0) \\
    & \to 3 * ((\fun n.\fun p.\ifZ p \Then 1 \Else n * (\fix f. G\ n (p - 1)))\ 3\ 0) \\
    & \to 3 * ((\fun p.\ifZ p \Then 1 \Else 3 * (\fix f. G\ 3 (p - 1)))\ 0) \\
    & \to 3 * (\ifZ 0 \Then 1 \Else 3 * (\fix f. G\ 3 (0 - 1))) \\
    & \to 3 * 1 \\
    & \to \fbox{3}
\end{align*}

2. Escribir un programa en PCF que tome un entero `n` y devuelva `1` si `n` es primo y `0` si no lo es. Calcular con `4` y con `3`.

\begin{align*}
    \textsf{EsPrimo} = \\
    & \fun n. \\
    & \Let g = \\
    & \fix f.\fun m.\fun x. \\
    & \ifZ (x - 1) \Then 1 \Else (\ifZ (m - (m \ x) * x) \Then 0 \Else (f\ m\ (x - 1))) \\
    & \In g\ n\ (n - 1)
\end{align*}

\begin{align*}
    \textsf{EsPrimo}\ 2 
    & = (\fun n.(\fix f.G)\ n\ (n - 1))\ 2 \\
    & \to (\fix f.G)\ 2\ (2 - 1) \\
    & \to (\fix f.G)\ 2\ 1 \\
    & \to (\fun m.\fun x.\ifZ (x - 1) \Then 1 \Else (\ifZ (m - (m \ x) * x) \Then 0 \Else ((\fix f. G)\ m (x - 1))))\ 2\ 1 \\
    & \to (\fun x.\ifZ (x - 1) \Then 1 \Else (\ifZ (2 - (2 \ x) * x) \Then 0 \Else ((\fix f. G)\ 2\ (x - 1))))\ 1 \\
    & \to \ifZ (1 - 1) \Then 1 \Else (\ifZ (2 - (2 \ 1) * 1) \Then 0 \Else ((\fix f. G)\ 2\ (1 - 1))) \\
    & \to^{*} \ifZ 0 \Then 1 \Else (\ifZ (2 - (2 \ 1) * 1) \Then 0 \Else ((\fix f. G)\ 2\ (1 - 1))) \\
    & \to \fbox{1}
\end{align*}

## Semántica operacional de PCF

\begin{align*}
    (\fun x.t)\ u &\rightarrow t[u/x] \\
    \ifZ 0 \Then t \Else u &\rightarrow t \\
    \ifZ n \Then t \Else u &\rightarrow t && \textsf{si}\ n \in \mathbb{Z} \\
    \fix x.t &\rightarrow t[\fix x.t/x] \\
    \Let x = t\ \In u &\rightarrow u[t/x]
\end{align*}

# De aca para abajo falta darle formato

## Congruencia
$
\dfrac{t \rightarrow u}{tv \rightarrow uv} \\
\dfrac{t \rightarrow u}{vt \rightarrow vu} \\
\dfrac{t \rightarrow u}{fun x.t \rightarrow fun x.u} \\
\dfrac{t \rightarrow t'}{ifz t then u else v \rightarrow ifz t' then u else v}
$

\textbf{No terminacion}

\begin{verbatim}
fix x.x -> x[fix x.x/x] = fix x.x
omega = (fun x.xx) (fun x.xx)
\end{verbatim}

Ejercicio

\begin{verbatim}
(fix f.fun x.f x) 0
-> (fun x.(fix f.fun x.f x) x) 0
-> (fix f.fun x.f x) 0
\end{verbatim}

\textbf{Captura de variables}

\begin{verbatim}
(fun x.fun y.(fun x.(x + y)) x) 5 4
-> (fun y.(fun x.(x + y)) 5) 4
-> (fun x.(x + 4)) 5
-> 5 + 4
-> 9

let x = 4
  in let f = fun y.y + x
    in let x = 5
      in f 6
      
-> let f = fun y.y + 4
     in let x = 5
       in f 6
       
-> let x = 5
     in (fun y. y + 4) 6

-> (fun y. y + 4) 6
-> 6 + 4
-> 10
     
\end{verbatim}

Definimos t[u/x] por inducción en t

$
x[u/x] = u \\
y[u/x] = y \\
(fun x.t)[u/x] = fun x.t \\
(fun y.t)[u/x] = fun y.t [u/x] si y \notin FV(u) \\
(fun y.t)[u/x] = fun z.t [z/y][u/x] si y \in FV(u) con z fresh variable \\
(t v)[u/x] = t[u/x] v[u/x] \\
n[u/x] = n \\
(t \otimes v)[u/x] = t[u/x] \otimes v[u/x] \\
(ifz t then v1 else v2)[u/x] = ifz t[u/x] then v1[u/x] else v2[u/x] \\
(fix x.t)[u/x] = fix x.t \\
(fix y.t)[u/x] = fix y.t [u/x] si y \notin FV(u) \\
(fix y.t)[u/x] = fix z.t [z/y][u/x] si y \in FV(u) con z fresh variable \\
(let x=t in v)[u/x] = let x = t[u/x] in v \\
(let y=t in v)[u/x] = let y = t[u/x] in v[u/x] si y \notin FV(u) \\
(let y=t in v)[u/x] = let y = t[u/x] in v[z/y][u/x] si y \in FV(u) con z fresh variable
$

Definimos FV(t) por inducción en t

$
FV(x) = {x} \\
FV(fun x t) = FV(t) - \{x\}
$
