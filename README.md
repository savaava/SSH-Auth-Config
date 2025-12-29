# Configurazione di Git per l'autenticazione SSH su GitHub

## Chiave SSH per GitHub
Se non si ha ancora una KeyPair SSH (chiave SSH privata e pubblica), è necessario crearne una per autenticarsi su GitHub.
1. Generare una nuova KeyPair SSH:
   ```bash
   ssh-keygen -t rsa # come minimo
   ssh-keygen -t rsa -b 4096 -C "tu@email.com" 
   # per specificare la lunghezza della chiave (4096 bit)
   # per maggiore chiarezza chiarezza (nome)
   ```
2. Invio per accettare il percorso predefinito:
    - **Su Linux e macOS:** `~/.ssh/id_rsa` e `~/.ssh/id_rsa.pub`
    - **Su Windows:** `C:\Users\UTENTE-PC\.ssh\id_rsa` e `C:\Users\UTENTE-PC\.ssh\id_rsa.pub`
3. Copiare la chiave pubblica:
   ```bash
   cat ~/.ssh/id_rsa.pub
   ```
   Un esempio della chiave da copiare (è un estratto falso, la chiave effettiva è molto più lunga):
   ```plaintext
    ssh-rsa DDDsL/SfgPVf8Da/Vqpe112NaXMC...QbHJigwaa== ciao@sonoio
   ```
4. Su [https://github.com/settings/keys](https://github.com/settings/keys) cliccare su **Add SSH key**
5. Incollare la chiave pubblica nel campo **Key** e dare un nome alla chiave.

## Configurazione dell'agente SSH (opzionale)
**L'agente SSH è un programma che gestisce le chiavi private per l'autenticazione**  
Permette di utilizzare le chiavi private senza doverle digitare ogni volta che si effettua una connessione SSH.
- Se la chiave privata NON ha passphrase, l’agente SSH non è indispensabile.
- Se la chiave privata HA una passphrase, l’agente SSH è utile per non doverla digitare ogni volta.
    - **Controllare che l'agente SSH sia attivo e abbia la chiave caricata**  
        ```bash
        ssh-add -l 
        ```
        Se l'output è vuoto, significa che non ci sono chiavi caricate nell'agente SSH.

        Avvia l'agente SSH (se non già attivo) e aggiungi la chiave privata:
        ```bash
        eval "$(ssh-agent -s)"
        ssh-add ~/.ssh/id_rsa
        ```

## Configurazione globale di Git  
   **Assicurarsi che nome utente e email siano corretti, ossia quelli associati al proprio account GitHub su cui è stata aggiunta la chiave SSH**:
- Per controllare i valori attuali della configurazione globale:
  ```bash
  git config --global --list
  ```
  Se non esiste il config file ```~/.gitconfig``` allora si devono impostare per la prima volta il nome e l'email:
- Per impostare correttamente il nome utente e l'email globali:
   ```bash
   git config --global user.name "savaava"
   git config --global user.email "andreasavastano4388@gmail.com"
   ```

## URL remoto per il repository
- Se la repo non è ancora stato clonata, **usare direttamente l'URL SSH della repo da GitHub** e non quello HTTPS:
   ```bash
   git clone git@github.com:{nome-utente}/{nome-repo}.git
   ```
- Se la repo è già stata clonata:
    1. **Controllare l'URL attuale del remote**  
        ```bash
        git remote -v
        ```
        Se l'output mostra un URL HTTPS, procedi al passo successivo. 
        Esempio di output:
        ```plaintext
        origin  https://github.com/savaava/MyTVsKeeper.git (fetch)
        origin  https://github.com/savaava/MyTVsKeeper.git (push)
        ```
    2. **Impostare il remote su SSH**
        ```bash
        git remote set-url origin git@github.com:utente/nome-repo.git
        ```
        ```bash
        git remote -v
        origin  git@github.com:savaava/MyTVsKeeper.git (fetch)
        origin  git@github.com:savaava/MyTVsKeeper.git (push)
        ```

## Conclusione
- **Testare la connessione SSH con GitHub**  
    ```bash
    ssh -T git@github.com
    ```
    Se tutto è configurato correttamente, vedrai un messaggio di benvenuto.
    ```plaintext
    Hi savaava! You've successfully authenticated, but GitHub does not provide shell access.
    ```
- **Ora è possibile eseguire `git fetch`, `git pull`, `git push`, ... con autenticazione automatica**
