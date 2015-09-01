\newcommand\fun{\textsf{fun}~}
\newcommand\fix{\textsf{fix}~}
\newcommand\ifZ{\textsf{ifZ}~}
\newcommand\Let{\textsf{let}~}
\newcommand\In{\textsf{in}~}
\newcommand\Then{~\textsf{then}~} % Uso mayúsculas para then y else porque en
\newcommand\Else{~\textsf{else}~} % minúsculas son palabras claves de LaTeX

Con lo que vimos hasta ahora de PCF, podemos escribir programas sin sentido, como los siguientes:

$$
    (\fun x.x + 1) (\fun x.x + 2)
$$

Para solucionar este problema, surgen los **tipos**. Los vamos a dar por definición inductiva:

- nat es un tipo
- si $A$ y $B$, entonces A => B es un tipo

Vamos entonces a "decorar" los parámetros con su tipo correspondiente:

$$
\fun x.t
\fun x:A.t

\fun x:nat.x
\fun x:nat=>nat.x
$$

## Gramática de PCF tipado
\begin{align*}
    \textsl{A}
    & = nat \\
    & | A => A
\end{align*}

\begin{align*}
    \textsl{t}
    & = x \\
    & | \fun x:A.t \\
    & | t t \\
    & | n \in \mathbb{Z} \\
    & | t + t | t - t | t * t | t / t \\
    & | \ifZ t \Then t \Else t \\
    & | \fix x:A.t \\
    & | \Let x:A = t ~ \In t
\end{align*}

Surge un problema cuando tenemos variables libres, por ejemplo en $ \fun x:nat.yx $, necesitamos saber el tipo de $y$. Para eso surgen los **contextos**, conjuntos de variables tipadas.

$$
z:nat, y:nat \Rightarrow nat \Rightarrow nat \vdash \fun x:nat.yzx: nat \Rightarrow A
$$

Tenemos que definir entonces $\gamma \vdash t:A$

\begin{align*}
    \gamma, x:A &\vdash x:A \\
    \gamma &\vdash n:nat \\
    \gamma &\vdash t:A \Rightarrow B \\
    
    \gamma &\vdash t:A \Rightarrow B           \gamma \vdash u:A
    \gamma \vdash t\ u:B

    \gamma x:A &\vdash t:B
    \gamma \vdash \fun x:A.t:A \RightArrow B

    \gamma &\vdash t:nat            \gamma \vdash u:nat
    \gamma \vdash t \otimes u:nat

    \gamma \vdash t:nat    \gamma \vdash u:A    \gamma \vdash v:A
    \gamma \vdash \ifZ t \Then u \Else v:A

    \gamma, x:A &\vdash t:B
    \gamma \vdash \fun x:A.t:A \RightArrow B    

    \infer{\vdash \Let x:a = t \In u:B}{\gamma \vdash t:a       \gamma, x:A \vdash u:B}
    \infer{\gamma \vdash \fix x:A.t:A}{\gamma, x:A \vdash t:A}
\end{align*}

