# MS DOS

## Comandi 

Per creare una cartella e spostarsi al suo interno, puoi utilizzare i seguenti comandi:
```shell
cd ..
md <nome_cartella>
cd <nome_cartella>
dir		
```

- Un progetto dotnet contiene più cartelle e file, tra cui:
  - `Program.cs`: il file principale che contiene il codice sorgente dell'applicazione.
  - `*.csproj`: il file di progetto che contiene le informazioni sulla configurazione del progetto.
  - `bin/` e `obj/`: cartelle generate automaticamente durante la compilazione del progetto.
- Una solutions di .NET è un contenitore per uno o più progetti .NET. 
	- Può essere utilizzata per organizzare e gestire più progetti correlati all'interno di un'unica soluzione.

Per realizzare una solutions di .NET, puoi utilizzare i seguenti comandi:
```shell
dotnet new sln
```
Il comando `dotnet` consente di creare (`dotnet new`), compilare (`dotnet build`), eseguire (`dotnet run`) e testare (`dotnet test`) un file .NET.

Per poter vedere un file testuale si usa il comando `type <nome_file>`. 
Per aprire un file di testo in un editor si può usare il comando `notepad <nome_file>`.

Per creare una nuova libreria di classi in .NET, puoi utilizzare il seguente comando:
```shell
dotnet new classlib <nome_libreria>
```
Per includere una libreria di classi in un progetto .NET, puoi utilizzare il seguente comando:
```bash
dotnet sln add <nome_libreria>/<nome_libreria>.csproj
```

Per poter utilizzare una particolare libreria di classi in un progetto .NET:
```bash
dotnet add reference ..\<percorso_libreria>\<nome_libreria>.csproj
```

Per compilare la soluzione di .NET, puoi utilizzare il seguente comando:
```shell
dotnet build
```
Per poter avere un unica cartella di output per tutti i progetti della soluzione, puoi modificare il file `.csproj` di ciascun progetto aggiungendo:
```xml
<OutDir>../bin</OutDir>
```

In Git, per poter tornare ad una versione precendente, situtilizza il comando:
```shell
git checkout <hash_commit>
```
dove `<hash_commit>` è l'identificatore univoco del commit a cui si desidera tornare.

Per ritornare alla versione più recente del ramo corrente, puoi utilizzare il comando:
```shell
git switch main
```

## Creare un programma di test usando MSTest

MSTest è un framework di test per .NET che consente di scrivere e eseguire test automatizzati per le applicazioni .NET. 
Per creare un programma di test utilizzando MSTest, puoi seguire questi passaggi:
```cmd
dotnet new mstest
```
successivamente: 
```cmd
dotnet sln add <folderTest>\<fileTest.csproj>
```
poi, per aggiungere le dipendenze del progetto da testare:
```cmd
dotnet add reference ..\<percorso_libreria>\<nome_libreria>.csproj
```

Infine, per poter testare il progetto, puoi utilizzare il seguente comando:
```cmd
dotnet test
```