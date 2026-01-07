# Numpy

---

## 1. Importazione e Setup

```python
import numpy as np

```

---

## 2. Creazione di Array

NumPy lavora con `ndarray` (N-dimensional array), molto più veloci ed efficienti delle liste Python.

```python
# Da una lista Python
arr = np.array([1, 2, 3])
matrice = np.array([[1, 2], [3, 4]]) // 2D

# Placeholder (utili per inizializzare)
np.zeros((3, 3))       # Matrice 3x3 di zeri
np.ones((2, 5))        # Matrice 2x5 di uni
np.full((2, 2), 99)    # Matrice 2x2 riempita col numero 99
np.eye(4)              # Matrice Identità 4x4

# Range numerici
np.arange(0, 10, 2)    # [0, 2, 4, 6, 8] (start, stop, step)
np.linspace(0, 1, 5)   # 5 numeri equamente spaziati tra 0 e 1 (utile per grafici)

# Numeri Casuali (Random)
np.random.rand(3, 2)       # Uniform distribution [0, 1)
np.random.randn(3, 2)      # Normal/Gaussian distribution (media 0, dev.std 1)
np.random.randint(1, 10, 5) # 5 interi casuali tra 1 e 10

```

---

## 3. Ispezione Array

Capire con cosa stai lavorando.

```python
arr = np.zeros((5, 4)) # 5 righe, 4 colonne

arr.shape    # (5, 4) -> Dimensioni
arr.ndim     # 2      -> Numero di dimensioni (assi)
arr.size     # 20     -> Numero totale di elementi
arr.dtype    # dtype('float64') -> Tipo di dato (int, float, bool...)

```

---

## 4. Indicizzazione e Slicing (Selezione)

Molto simile alle liste Python, ma più potente per le dimensioni multiple.

```python
arr = np.array([[10, 20, 30], [40, 50, 60]])

# Accesso singolo
x = arr[0, 2]       # Riga 0, Colonna 2 -> Risultato: 30

# Slicing [start:stop:step]
row = arr[0, :]     # Tutta la riga 0 -> [10, 20, 30]
col = arr[:, 1]     # Tutta la colonna 1 -> [20, 50]
sub = arr[0:2, 1:3] # Sotto-matrice -> [[20, 30], [50, 60]]

# Boolean Indexing (Maschere) - POTENTISSIMO
filtro = arr[arr > 35] # Seleziona solo elementi > 35 -> [40, 50, 60]

```

---

## 5. Operazioni Matematiche e Statistiche

NumPy usa la **vettorializzazione**: esegue operazioni su interi array senza bisogno di cicli `for` (molto veloce).

```python
a = np.array([1, 2, 3])
b = np.array([4, 5, 6])

# Operazioni Element-wise (elemento per elemento)
somma = a + b      # [5, 7, 9]
prod  = a * b      # [4, 10, 18]
potenza = a ** 2   # [1, 4, 9]

# Statistiche (Aggregazioni)
arr = np.array([[1, 2, 3], [4, 5, 6]])

arr.sum()          # Somma totale: 21
arr.mean()         # Media: 3.5
arr.std()          # Deviazione Standard
arr.min() / arr.max() 

# Importante: AXIS (0 = colonne/verticale, 1 = righe/orizzontale)
arr.sum(axis=0)    # Somma schiacciando le righe -> [5, 7, 9]
arr.sum(axis=1)    # Somma schiacciando le colonne -> [6, 15]

```

---

## 6. Manipolazione Forme (Reshaping)

Modificare la struttura dei dati senza cambiarne il contenuto.

```python
arr = np.arange(12)  # [0, 1, ..., 11]

# Reshape
mat = arr.reshape(3, 4)  # Trasforma in griglia 3x4

# Appiattimento
flat = mat.flatten()     # Ritorna array 1D (copia dei dati)
ravel = mat.ravel()      # Ritorna array 1D (vista/referenza, più veloce)

# Transpose (Scambia righe con colonne)
t = mat.T                # Oppure mat.transpose()

```

---

## 7. Unione e Divisione (Stacking & Splitting)

Combinare array multipli.

```python
a = np.array([1, 2])
b = np.array([3, 4])

# Concatenazione
np.concatenate((a, b)) # [1, 2, 3, 4]

# Stacking (più intuitivo per 2D)
np.vstack((a, b))  # Vertical Stack: [[1, 2], [3, 4]]
np.hstack((a, b))  # Horizontal Stack: [1, 2, 3, 4]

```

---

## 8. Algebra Lineare

Fondamentale per Machine Learning e Data Science.

```python
A = np.array([[1, 2], [3, 4]])
B = np.array([[5, 6], [7, 8]])

# Prodotto Matriciale (Dot Product)
# Riga x Colonna
P = np.dot(A, B)   
# Oppure operatore moderno (Python 3.5+)
P = A @ B 

```

---

## 9. Funzioni di Utilità (Logic & Search)

Metodi molto usati per pulizia dati e logica condizionale.

```python
arr = np.array([10, 20, 30, 40, 50])

# np.where (L'equivalente vettorializzato di "if... else...")
# Sintassi: np.where(condizione, valore_se_vero, valore_se_falso)
res = np.where(arr > 30, 'Alto', 'Basso') 
# Output: ['Basso', 'Basso', 'Basso', 'Alto', 'Alto']

# np.unique (Valori univoci)
valori_unici = np.unique([1, 1, 2, 2, 3]) # [1, 2, 3]

# np.argsort (Ritorna gli INDICI che ordinerebbero l'array)
indici = np.argsort(arr)

```

---

## 10. Broadcasting (Concetto Chiave)

Il broadcasting permette a NumPy di eseguire operazioni matematiche su array di forme diverse (es. sommare uno scalare a una matrice).

```python
a = np.array([1, 2, 3]) # Shape (3,)
b = 2                   # Scalare

# Broadcasting: 'b' viene virtualmente esteso a [2, 2, 2] per matchare 'a'
print(a * b) # [2, 4, 6]

# Esempio 2D
matrice = np.ones((3, 3)) # 3x3
riga = np.array([0, 1, 2]) # 1x3
# La riga viene sommata a OGNI riga della matrice
print(matrice + riga)

```
