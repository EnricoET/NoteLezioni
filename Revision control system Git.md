# Appunti Git e IL

## Revision control system: Git ---
- Problema: si lavora i team e si vuole essere sicuri di utilizzare la versione corretta del codice del progetto senza sovrascrivere le impostazioni globali del sistema. 
- Soluzione: uso di un sistema di versionamento, come Git, che consente di gestire le modifiche al codice sorgente in modo collaborativo e sicuro.

Nel caso si apportino modifiche al file di progetto, attraverso il controllo di versione si ottiene che tali modifiche possano essere facilmente tracciate e gestite.
Il codice è inviato in una Central Repository e da esso ciascuno può leggere e scrivere le modifiche. 
Git è un sistema di versionamento distribuito, ovvero ogni sviluppatore ha una copia completa del repository sul proprio computer, e può lavorare in modo indipendente senza dover essere connesso al server centrale.

Tipico workflow: 
1. Il *lead developer* crea un nuovo branch, ovvero una copia del codice sorgente principale
2. Gli sviluppatori lavorano sulle loro modifiche nel branch creato
3. Una volta completate le modifiche, gli sviluppatori fanno il push delle loro modifiche nel branch remoto
4. Il *lead developer* esegue il pull delle modifiche dal branch remoto e le integra nel branch principale
    - Con una pull request, chiedo al *lead developer* di visionare le mie modifiche e di integrarle nel branch principale per eseguire un merge. 
    - Per poter far capire il contesto e le modifiche effettuate si eseguono dei commenti sulle modifiche proposte.
    - IL sistema di versione consente di visualizzare le differenze tra il codice sorgente originale e quello modificato, evidenziando le linee aggiunte, modificate o rimosse.
     - Il lead developer può approvare o rifiutare la pull request in base alla qualità del codice e alla conformità alle linee guida del progetto.

COMANDI UTILI: `git init - git status - git diff - git add . - git commit -m "" `

## IL : Intermediate Language ---
Una *.NET Application* prevede 3 layer: 
```mermaid
graph LR
    A[Codice C#] --> B[compilazione in IL]
    B --> |JIT compiler| C[codice macchina]
```

*OSS: i primi 2 layer sono uguali per tutti, il terzo relativo al JIT (just in time) compiler varia*

Perché è strutturato in tale maniera ? -> Il *.net development framework* deve essere simile a Java, dunque può essere utilizzato ovunque -> l'eseguibile è neutrale e utilizzabile in piattaforme intel, arm , ecc.

Uso di LINQPad come *decompiler* per analizzare il funzionamento del framework .net:
- è possibile vedere i 3 layer di una applicazione .net, il codice sorgente in C#, il codice intermedio IL e il codice macchina generato dal JIT compiler.
- Per un processore intel/amd è presente un ulteriore layer nascosto, chiamato intel microcode -> traduce il codice del JIT compiler basato sull'instrction set AMDx64 in istruzioni comprensibili da relativo processore intel/amd.
    - La presenza di un ulteriore layer è dovuta all'architettura CISC (complex instruction set computer) -> codice più corto ma più complesso da eseguire e molto più lento
  - Per un processore ARM (es. M1) non è presente un ulteriore layer di microcode, ma esegue direttamente le istruzioni del JIT compiler basate sull'instruction set ARM64.
    - Rispetto all'architettura CISC, l'architettura ARM è RISC (reduced instruction set computer) -> codice più lungo ma più semplice da eseguire e molto più veloce

### Esempi di IL
L'IL suppone di avere un processore virtuale che opera con uno standard default *Operand Stack*  polyformico che consente di avere dati ti tutti i tipi.
Per poter salvare dei parametri e variabili di una funzione si usa un altro stack virtuale, chiamato *Local Variables Stack (Stack Frame)* 
#### - Esempio 1
```csharp 
// NOTA. tutto chiò che inizia con "." è una direttiva
// Importazione della libreria mscorlib.dll, che contiene le classi di base del framework .NET

.assembly extern mscorlib { } 

// NOTA: un assembly può essere una libreria (.dll) oppure un eseguibile (.exe) 
// Dichiaro ora di creare un assembly chiamato Test, che conterrà il codice IL del programma 

.assembly Test { }

.method static void Test(string[] args) {
    .entrypoint 
    // carico una costante intera di 4 byte ( ovvero int 32) nello stack
    ldc.i4 10
    conv.r8
    ldc.r8 5.5
    add
    // chiamo il metodo WriteLine della classe Console
    call void [mscorlib]System.Console::WriteLine(float64) 
    ret
}
```

#### - Esempio 2
```il 
.assembly extern mscorlib { } 

.assembly Test { }

.method static void Test(string[] args) {
    .entrypoint 
    // creo una variabile locale
    .locals init ([0] int32 i)
    ldc.i4 0
    stloc i
    br CHECK
LOOP:
    ldloca i
    call instance string [mscorlib]System.Int32::ToString()
    ldstr " "
    call string [mscorlib]System.String::Concat(string, string)
    call void [mscorlib]System.Console::Write(string) 

NEXT:
    ldloc i
    ldc.i4 1
    add
    stloc i

CHECK:
    ldloc i
    ldc.i4 10
    blt LOOP
    ret
}
``` 

#### - Esempio 3
```il
.assembly extern mscorlib { } 

.assembly Test { }

.method static void Test(string[] args) {
    .entrypoint 
    newobj instance void Animal::.ctor() 
    dup 
    dup
    dup
    ldstr "Tom"
    stfld string Animal::Name
    lc.i4 12
    stdfld int 32 Animal::Age

    ldc.i4 5
    call instance void Animal::Grow(int32)

    call instance string Animal::ToString()
    call void [mscorlib]System.Console::WriteLine(string)
    ret
}

.class Animal {
    // campi della classe
    .field public string Name
    .field public int32 Age
    
    // costruttore della classe
    .method public void .ctor() {
        ldarg 0
        call instance void [mscorlib]System.Object::.ctor()
        ret
    }

    .method public virtual string ToString () {
        ldstr "Animal: "
        ldarg 0
        ldfld string Animal::Name
        ldstr ","
        ldarg 0
        ldfld int32 Animal::Age    
        call instance string [mscorlib]System.String::ToString()
        call instance string [mscorlib]System.String::Concat(string, string, string, string)    
        ret
    }

    .method public void Grow ( int32 a) {
        ldarg 0
        dup
        ldfld int32 Animal::Age
        ldarg da
        add
        stfld int32 Animal::Age
    } 
}

``` 

#### - Esempio 4
```il
.assembly extern mscorlib { } 

.assembly Test { }

.method static void Test(string[] args) {
    .entrypoint 
    .locals ([0] int32 i)
    ldc.i4 2
    stloc i
    br CHECK
LOOP:
    ldloca i
    dup
    call bool IsPrime(int32)
    brfalse SKIP
    call void [mscorlib]System.Console::WriteLine(int32)

SKIP:
    ldloc i
    ldc.i4 1
    add
    stloc i
     
CHECK:
    Idloc i
    Idc.i4 10
    blt LOOP
    ret
} 

.method static bool IsPrime (int32 n) {
    .locals ([0] int32 j)
    ldc.i4 2
    stloc j
    br CHECK            // i = 2
LOOP:
    ldarg n
    ldloc j
    rem
    brzero NOTPRIME     // if j % n == 0 -> not prime
    ldloc j
    ldc.i4 1
    add
    stloc j             //j++

CHECK:
    ldloc j
    ldarg n
    blt LOOP            // Loop While j < n
    ldc.i4 1
    ret

NOTPRIME:
    ldc.i4 0
    ret
}
``` 