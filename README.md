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
    Map <-- Territoy    

    RuleChecker <|-- MoveInputChecker
    RuleChecker <|-- MovePathChecker
    RuleChecker <|-- AttackInputChecker
    RuleChecker <|-- AttackAdjacentChecker

    note for Map "A map has N territoies (N >= 6) owned by K players (2<=K<=4)
        "
   
    class Map {
        +Map~Territoy~ Territoy
    }

    class Territoy {
        +String name
        +Player owner
        +Set~Territoy~ neighbours
        +int units
        +getName():String
        +getOwner():Player
        +getNeighbours():Set~Territoy~
        +getNumofUnits():int 
    }

    class RuleChecker {
        -RuleChecker<T> next
        #checkMyRule()
    }

    class MoveInputChecker{
        #checkMyRule():\n//check units > 0 \n //check source territory is your.\n //check destinination territory is your.
    }

     class MovePathChecker{
        #checkMyRule()
    }

    class AttackInputChecker{
        #checkMyRule():\n//check units > 0 \n //check source territory is your.\n //check destinination territory is your.
    }

    class AttackAdjacentChecker{
        #checkMyRule()
    }
```
## Implementation
