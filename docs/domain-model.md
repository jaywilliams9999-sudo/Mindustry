# Mindustry Domain Model

Conceptual model of the *game world* Mindustry simulates (not the code).
Attributes only: no methods, types, or visibility markers.

Updated for A2b (SSDs and Operation Contracts): added the campaign and multiplayer
concepts named in the three fully-dressed use cases (Launch to Sector, Research
Technology, Join Multiplayer Game).

```mermaid
classDiagram
    class Team {
        name
        color
    }
    class Player {
        name
        color
        gameVersion
        admin
    }
    class Unit {
        health
        position
    }
    class Structure {
        health
        size
        position
    }
    class Core {
        storageCapacity
    }
    class Resource {
        name
    }
    class Stock {
        quantity
    }
    class Wave {
        number
        countdown
    }
    class SpawnPoint {
        position
    }
    class Planet {
        name
    }
    class Sector {
        name
        captured
        locked
        threat
    }
    class Map {
        name
    }
    class Loadout {
        coreType
    }
    class ItemCost {
        amount
    }
    class Technology {
        name
        unlocked
    }
    class ResearchCost {
        amount
        amountPaid
    }
    class Objective {
        description
        complete
    }
    class Server {
        name
        address
        version
        playerLimit
    }
    class Mod {
        name
        version
    }

    Structure <|-- Core

    Team "1" -- "0..*" Player : fights for
    Player "0..1" -- "0..1" Unit : controls
    Team "1" -- "0..*" Unit : commands
    Team "1" -- "0..*" Structure : owns
    Structure "0..*" -- "1..*" Resource : costs
    Stock "0..*" -- "1" Resource : amount of
    Team "1" -- "0..*" Wave : sends
    Wave "0..1" -- "0..*" Unit : spawns
    Wave "0..*" -- "1..*" SpawnPoint : enters at

    Planet "1" -- "1..*" Sector : divided into
    Sector "0..*" -- "0..*" Sector : is next to
    Sector "0..*" -- "0..1" Sector : was launched from
    Sector "1" -- "1" Map : has terrain
    Sector "1" -- "0..*" Structure : contains
    Sector "1" -- "0..*" Stock : holds
    Team "1" -- "0..*" Sector : has captured

    Loadout "0..*" -- "1..*" ItemCost : costs
    ItemCost "0..*" -- "1" Resource : amount of

    Planet "1" -- "1..*" Technology : has tech tree of
    Technology "0..*" -- "0..1" Technology : requires
    Technology "1" -- "0..*" ResearchCost : costs
    ResearchCost "0..*" -- "1" Resource : amount of
    Technology "1" -- "0..*" Objective : requires

    Server "0..*" -- "1" Map : is running
    Player "0..*" -- "0..1" Server : is connected to
    Player "0..*" -- "0..*" Mod : has installed
    Server "0..*" -- "0..*" Mod : requires
```

## Notes

- **Stock moved from Core to Sector.** In the campaign, items belong to a sector
  (the Research use case totals items "across all captured sectors"). While a sector
  is being played they are physically in its Core, but conceptually the Sector holds them.
- **ItemCost vs ResearchCost.** A Loadout's cost is paid all at once. A Technology's
  cost can be paid in installments, so each ResearchCost also records `amountPaid`.
- **Player** means the in-game player: the one fighting for a Team and, in multiplayer, the
  one connected to a Server. The person at the keyboard is the *actor* Player in the SSDs.
