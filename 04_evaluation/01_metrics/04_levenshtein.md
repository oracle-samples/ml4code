# Levenshtein Edit Similarity
Leveshtein distance is a string metric for measuring the difference between two sequences. Informally, it counts the number of edits necessary to turn one string into the other.

Considering two string $a$, $b$, it is formally defined as

$$
mathrm{lev}(a,b)=\left\\{
\begin{array}{ll}
    |a|\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad \text{ if } |b|=0, \\\
    |b|\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad \text{ if }|a|=0, \\\
    mathrm{lev}(mathrm{tail}(a), mathrm{tail}(b) \quad\quad\quad\quad\quad\quad\quad\text{if }a[0]=b[0], \\\
    1+\min\left\\{
    \begin{array}{ll}
        mathrm{lev}(mathrm{tail}(a), b) \\\
        mathrm{lev}(a, mathrm{tail}(b)) \\\
        mathrm{lev}(mathrm{tail}(a), mathrm{tail}(b)
    \end{array}
    \right.
    \quad\text{otherwise}
\end{array}
\right.
$$
where $mathrm{tail}(x)$ is a string of all but the first character of $x$.