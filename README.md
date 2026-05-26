# Simulatore F-16 in C++

Questo progetto è un simulatore di volo scritto in C++ moderno (C++17) che modella la fisica, la cinematica e le leggi di controllo (Fly-by-Wire) per un velivolo F-16. Il progetto impiega un motore grafico leggero per il rendering 3D e fa uso del protocollo DDS per la distribuzione in tempo reale dei dati di telemetria.

## Architettura del Sistema

Il sistema si compone di moduli e applicativi indipendenti che interagiscono tra loro:

- **FlightSim**: L'eseguibile principale che racchiude il cuore del simulatore. Comprende il Flight Control Computer (FCC) per la logica Fly-by-Wire, l'elaborazione dei comandi del pilota (input) e il calcolo in tempo reale di aerodinamica, cinematica e stato degli attuatori (a 60 FPS). Include anche il rendering grafico a schermo dei parametri di volo e dell'ambiente 3D circostante.
- **MonitorApp**: Un'applicazione separata che funge da stazione di monitoraggio (Ground Station). Si iscrive ai topic DDS per ricevere i dati telemetrici trasmessi dal simulatore e li visualizza a schermo tramite un'interfaccia dedicata.
- **Livello di Comunicazione (Fast DDS)**: I due eseguibili scambiano dati attraverso `eProsima Fast DDS` sfruttando il paradigma Publisher/Subscriber. I principali topic pubblicati sono:
  - `F16KinematicsTopic`: Posizione, orientamento e ratei angolari.
  - `F16AeroStateTopic`: Variabili aerodinamiche come velocità (Mach, Nodi), angolo d'attacco (alpha), fattore di carico (nz) e stato del sistema.
  - `F16ActuatorsTopic`: Deflessione delle superfici di controllo (equilibratori, alettoni, timone, ecc.) e comando motore.
- **Grafica e Input (Raylib)**: Entrambe le applicazioni utilizzano la libreria `raylib` per disegnare l'interfaccia a schermo, gestire gli input da tastiera/joystick e riprodurre gli effetti sonori.

## Struttura delle Cartelle

- `src/`: Contiene i sorgenti C++ dell'applicativo (logica FBW, gestione DDS, core del simulatore e delle schermate di visualizzazione).
- `raylib/`: File della libreria raylib (se compilata localmente) o header necessari.
- `resources/`: Asset utilizzati per il rendering grafico e audio del simulatore (es. modelli 3D in formato `.glb`, file sonori `.wav`).
- `DocumentazioneSimualtore/`: Documentazione estesa, note progettuali o manuali relativi al funzionamento del simulatore.

## Requisiti di Sistema

Per compilare ed eseguire il progetto è necessario avere installato nel proprio ambiente:

- **Compilatore C++17** (GCC, Clang, MSVC)
- **CMake** (versione 3.10 o superiore)
- **eProsima Fast DDS** e **Fast CDR** (solitamente installati in `/home/leonardo/eprosima_install` o percorsi globali di sistema)
- Libreria **raylib** (e relative dipendenze grafiche come OpenGL, pthread, X11/Wayland su Linux)

## Compilazione

Il progetto utilizza CMake come sistema di build. Dalla cartella contenente il `CMakeLists.txt` (sotto `Simulatorec++`), è possibile configurare e compilare il simulatore eseguendo:

```bash
mkdir build
cd build
cmake ..
cmake --build .
```

Il processo di build si occuperà anche di copiare automaticamente la cartella degli asset (`resources/`) all'interno della cartella di build in modo che il simulatore possa caricare correttamente l'ambiente, i modelli 3D dell'aereo e i suoni.

## Esecuzione

Dopo aver compilato, all'interno della directory `build` potrai avviare i due applicativi in terminali separati:

1. **Simulatore Principale:**
   ```bash
   ./FlightSim
   ```

2. **Ground Station / Monitoraggio:**
   ```bash
   ./MonitorApp
   ```
