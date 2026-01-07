# 🧠 Tabella dei Comandi Git Essenziali

| **Categoria** | **Comando Git** | **Descrizione / Utilizzo** |
|----------------|------------------|-----------------------------|
| 🧩 **Configurazione** | `git config --global user.name "Nome"` | Imposta il nome utente globale per i commit |
| | `git config --global user.email "email@example.com"` | Imposta l’email usata nei commit |
| | `git config --list` | Mostra tutte le configurazioni attive |
| 🏗️ **Creazione / Clonazione** | `git init` | Inizializza un nuovo repository locale |
| | `git clone <url>` | Clona un repository remoto in locale |
| 📂 **Stato e modifiche** | `git status` | Mostra lo stato dei file (modificati, nuovi, ecc.) |
| | `git add <file>` | Aggiunge un file all’area di staging |
| | `git add .` | Aggiunge **tutti** i file modificati |
| | `git restore --staged <file>` | Rimuove un file dallo staging (senza cancellarlo) |
| | `git diff` | Mostra le differenze tra i file modificati e l’ultimo commit |
| 💾 **Commit** | `git commit -m "messaggio"` | Crea un nuovo commit con il messaggio indicato |
| | `git commit --amend` | Modifica l’ultimo commit (messaggio o contenuto) |
| 🌳 **Branch** | `git branch` | Mostra tutte le branch locali |
| | `git branch -a` | Mostra branch locali e remote |
| | `git branch <nome>` | Crea una nuova branch |
| | `git checkout <nome>` | Passa a un’altra branch |
| | `git switch -c <nome>` | Crea e passa a una nuova branch (comando moderno) |
| | `git branch -d <nome>` | Elimina una branch locale (se mergiata) |
| | `git branch --track <nuovo_branch_locale> <branch_remoto>` | Crea una branch locale collegata ad una remota. |
| | `git branch -D <nome>` | Elimina una branch locale forzatamente |
| 🔁 **Merge e Rebase** | `git merge <branch>` | Unisce un’altra branch in quella corrente |
| | `git merge --abort` | Annulla un merge in corso |
| | `git rebase <branch>` | Reapplica i commit sopra un’altra branch (storia lineare) |
| | `git rebase --continue` | Continua il rebase dopo aver risolto un conflitto |
| ⚔️ **Conflitti** | `git status` | Mostra i file in conflitto |
| | `git add <file>` | Segna un conflitto come risolto |
| | `git commit` | Conclude il merge dopo aver risolto i conflitti |
| | `git mergetool` | Apre un tool grafico per risolvere conflitti |
| 🚀 **Remote (repo remoti)** | `git remote -v` | Mostra i repository remoti configurati |
| | `git remote add origin <url>` | Collega il repo locale a un remoto |
| | `git fetch --all` | Scarica aggiornamenti e branch dal remoto |
| | `git pull` | Scarica e integra le modifiche dal remoto |
| | `git push` | Invia i commit locali al remoto |
| | `git push -u origin <branch>` | Pusha una branch e la collega al remoto |
| | `git push origin --delete <branch>` | Elimina una branch remota |
| ⏳ **Cronologia e log** | `git log` | Mostra la cronologia completa dei commit |
| | `git log --oneline` | Mostra la cronologia compatta (un commit per riga) |
| | `git log --oneline --graph --all` | Mostra la cronologia come grafico |
| | `git diff <branch1>..<branch2>` | Mostra differenze tra due branch |
| 🧹 **Ripristino e reset** | `git restore <file>` | Ripristina un file alla versione dell’ultimo commit |
| | `git reset --soft HEAD~1` | Annulla l’ultimo commit ma mantiene le modifiche |
| | `git reset --hard HEAD~1` | Annulla l’ultimo commit **e** le modifiche |
| | `git revert <hash>` | Crea un commit che annulla un commit specifico |
| 🏷️ **Tag e versioni** | `git tag` | Elenca tutti i tag esistenti |
| | `git tag -a v1.0.0 -m "descrizione"` | Crea un tag annotato (es. per una release) |
| | `git push origin --tags` | Pusha tutti i tag sul remoto |
| 🧠 **Info utili** | `git show <hash>` | Mostra i dettagli di un commit specifico |
| | `git blame <file>` | Mostra chi ha modificato ogni riga di un file |
| | `git clean -fd` | Rimuove file e cartelle non tracciati |
| 🧭 **Tracciamento remoto / sincronizzazione** | `git fetch` | Aggiorna le branch remote senza unirle |
| | `git branch -vv` | Mostra quali branch locali seguono quali remote |
| | `git checkout -b <branch> origin/<branch>` | Crea una branch locale collegata a una remota |
| | `git remote prune origin` | Rimuove branch remote eliminate dal server |

---

## 💡 Suggerimenti rapidi

| Azione | Comando |
|--------|----------|
| Aggiornare tutte le branch remote | `git fetch --all` |
| Fare merge di `main` in `production` | `git checkout production && git merge main` |
| Eliminare una branch dopo il merge | `git branch -d <nome>` |
| Creare una nuova feature branch | `git checkout -b feature/<nome>` |
| Visualizzare graficamente la storia | `git log --oneline --graph --all` |

---

## 📘 Note finali
- Usa `git status` spesso — è il tuo migliore alleato.  
- Fai `git pull` **prima** di iniziare a lavorare.  
- Evita di fare `push` diretti su branch come `production`: usa merge o pull request.  
- Mantieni i commit **piccoli e descrittivi**.  
- Fai `git fetch --all` quando lavori su più macchine per sincronizzare le branch.

---
