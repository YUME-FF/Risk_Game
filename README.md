# Risk_Game

# Table of Contents

- [Review](#Review)
  - [Requirements](#Requirements)
- [Design Overview](#Design-Overview)
  - [UML Diagram](#UML-Diagram)
- [Implementation](#Implementation)

## Review

### Requirements

See requirements in [prj1.pdf](prj1.pdf)

## Design Overview

### UML Diagram
```mermaid
classDiagram
    Map "1" --> "N" Territory
    Server "1" --> "N" Map
    Server "1" --> "1" RuleChecker
    Server -- Client : Link
    Server --> ActionHandle
    Server --> mapFactory
    ActionHandle --> Action
    Client --> Unit
    Client "1" --> "N" Territory
    Game "1" --> "1" Server
    Game "1" --> "N" Client
    Displayable <|.. mapTextView
    Displayable <|.. mapGUIView

    Displayable <-- Client

    RuleChecker <|-- MoveInputChecker
    RuleChecker <|-- MovePathChecker
    RuleChecker <|-- AttackInputChecker
    RuleChecker <|-- AttackAdjacentChecker

    AttackAction --|> Action
    MoveAction --|> Action

    class Game {
        -Server server
        -Client client
        -String phase
        +main(args: String[])
        
    }
    
    class Server{
        -String port
        +checkRule()
        +checkWin(): Client
        +makeMap(): Map
        +sendMap(Client c,Map m): void
        +unitPlacement(): void
        +checkifCommit(): boolean
        +distributeTerritoryGroups(Map m): void
        +listen(): void
        +doPlacementPhase(): void
        +doMovePhase(): void
        +doAttackPhase(): void
        +processOneMove() //
        +processOneAttack()
        +automaticCommit(): void
    }
    
    class Client{
        -boolean ifLost
        -string color
        +displayMap(): Displayable
        +unitPlacement(): Displayable
        +exit(): void
        +connectServer(): void
        +chooseTerritoryGroup(Map m): void
        +checkLost(): boolean
        +setColor(String c): void
        +addTerritory(Territory t): void
        +sendMoveOrder(Territory from, Territory to, int unit): void
        +sendAttackOrder(Territory from, Territory to, int unit): void
        +commitMove(): void
        +displayWin(): void
    }

    class Map {
        -HashMap~Territory, Client~ map
    }


    class mapFactory{
      <<interface>>
    }

    class Territory {
        -String name
        -Set~Territory~ neighbours
        -int units
        +getName():String
        +getNeighbours():Set~Territory~
        +getNumofUnits():int 
    }

    class Unit{
        Client owner
        Territory pos
    }

    class Action {
        -Client Client
        -int unit
        -Territory from
        -Territory to 
        -Rulechecker checker
        +getClient(): Client    
    }

    class MoveAction{
        + tryMove(): boolean
    }

    class AttackAction{
        + tryAttack(): boolean
    }

    class Displayable{
    <<interface>>
        +Display(): void
    }

    class mapTextView{
    }

    class mapGUIView{
    }

    class RuleChecker {
        -RuleChecker<T> next
        #checkMyRule()
    }

    class MoveInputChecker{
        #checkMyRule():
    }

     class MovePathChecker{
        #checkMyRule():
    }

    class AttackInputChecker{
        #checkMyRule():
    }

    class AttackAdjacentChecker{
        #checkMyRule():
    }

```
## Implementation
