# Risk_Game

# Table of Contents
- [Coverage](#Coverage)
- [Schedule](#Schedule)
- [Requirements](#Requirements)
- [Game Detail](#Game-Detail)
  - [Duke Map](#Duke-Map)
  - [Map initialization](#Map-initialization)
  - [Unit initialization](#Unit-initialization)
  - [Resource and Tech Level initialization](#Resource-and-Tech-Level-initialization)
  - [Action Costs](#Action-Costs)
- [Design Overview](#Design-Overview)
  - [UML Diagram](#UML-Diagram)
  - [UI Prototype](#UI-Prototype)

## Schedule
* Evolution 1: 2/24 - 3/24
* Evolution 2: 3/24 - 4/10
* Evoluiton 3: 4/10 - 4/28

[Project Mamagement Sheet](https://docs.google.com/spreadsheets/d/1B2cUguqBPXm3IJBRU1WRPnoBng4p8AiTouAkxmXUhYg/edit#gid=0)

## Requirements
[Evolution1](prj1.pdf)
[Evolution2](prj2.pdf)
[Evolution3](prj3.pdf)


## Game Detail

### Map initialization
The duke map is initialized with 24 territories. This game supports 2-4 players. For 2 players, each player will have 12 territories, and can choose from colors: red, blue. For 3 players, each player will have 8 territories, and can choose from colors: red, blue, yellow. For 4 players, each player will have 6 territories, and can choose from colors: red, blue, yellow, green. For each pair of adjacent territories, the distance between them is 5.

### Unit initialization
The player each has 24 First-year students(level 0) at the start of the game to assign to their territories. There is a total of 7 level of students: First-year(level 0), Second-year(level 1), Third-year(level 2), Fourth-year(level 3), Fifth-year(level 4), Sixth-year(level 5), Seventh-year(level 6). Each territory will generate 1 First-year student(level 0) at the beginning of a new turn.

### Resource and Tech Level initialization
Each territory produces 5 food resources and 5 tech resources per turn. Each player starts with a tech level 1, and can upgrade per turn till tech level 6.

### Action Costs
Attack and move actions cost food resources.
* Attack cost = 8 * numUnits
* Move cost =  distance * numUnits

Research and upgrade actions cost tech resources.
* Research Cost Table

| Upgrade Level | Cost |
| ------------- | ---- |
|     1->2      |  20  |
|     2->3      |  40  |
|     3->4      |  80  |
|     4->5      |  160 |
|     5->6      |  320 |

* Upgrade Cost+Bonus Table

| Cost (Total) | Bonus | Tech Level Required |
| ------------ | ----- | ------------------- |
|     0(0)     |   0   |   Units Start Here  |
|     3(3)     |   1   |          1          |
|     8(11)    |   3   |          2          |
|    19(30)    |   5   |          3          |
|    25(55)    |   8   |          4          |
|    35(90)    |   11  |          5          |
|    50(140)   |   15  |          6          |

## Design Overview
### UML Diagram
```mermaid
classDiagram
    ServerApp "1" --* "1" Server
    ClientApp "1" --* "N" Client
    Client "1" --* "1" Views
    Server "1" --* "1" Game
    Game "1" --* "N" Player
    Game "1" --* "N" Order
    Game "1" --* "1" MessageGenerator
    Game "1" --* "1" GameMap
    Game "1" --* "N" Conn 
    Player "1" --> "1" Conn
    Player "1"--> "N" Territory
    Player "1" --* "1" AssignUnitRuleChecker
    Player "1" --* "N" Unit
    Territory "1"--> "N" Unit
    Unit <|.. BasicUnit
    AttackOrder --* OrderRuleChecker
    MoveOrder --* OrderRuleChecker

    
    AttackOrder --|> Order
    MoveOrder --|> Order
    ResearchOrder --|> Order
    UpgradeOrder --|> Order

    Territory "1" --* "0..1" Battle
    Battle --* Combat
    
    OrderRuleChecker <|-- MoveInputRuleChecker
    OrderRuleChecker <|-- MovePathRuleChecker
    OrderRuleChecker <|-- AttackInputRuleChecker
    OrderRuleChecker <|-- AttackAdjacentRuleChecker



class ClientApp {
    + main(String[] args): void
}

class Client {
    - BufferedReader socketReceive
    - PrintWriter socketSend
    - Views view
    - PrintStream out
    - BufferedReader inputReader
    - boolean ifExit
    + run(): void
    + ifExit(): boolean
    + runWatchOption(): void
    + recvMsg(): String
    + readClientInput(String prompt): String
    + sendResponse(String response): void
    + waitEveryoneDone(): void
    + playerChooseColor(): void
    + playerChooseNum(): void
    + playerAssignUnit(): boolean
    + playerAssignUnits(): void
    + playerActionTurn(): void
    + playerOneAction(): void
    + checkMoveInputFormat(String clientInput): boolean
    + playerChooseWatch(): void
}



class Views {
    - PrintStream out
    - boolean watch
    - ObjectMapper objectMapper
    + setWatch(): void
    + isWatch(): boolean
    + displayInitialMap(String jsonString): void
    + displayEntry(String to_display): String
    + displayMap(String jsonString): void
    + displayLog(String jsonString): void
    + playerWatchTurn(): void
}


class ServerApp {
    - int port
    - ServerSocket listenSocket
    - Server server
    + createServerSocket(): void
    + createServer(): void
    + runServer(): void
    + main(String[] args): void
}




class Server {
    - ServerSocket listenSocket
    - ThreadPoolExecutor threadPool
    - Game game
    - int numClients
    + Server(ServerSocket: serverSocket)
    + acceptOrNull(): Socket
    + run(): void
}

class MessageGenerator {
    - ObjectMapper objectMapper
    + formEntry(Player p): HashMap<String, String>
    + sendEntry(Player p): void
    + sendLog(Player p, HashMap<String, String> to_send): void
    + sendInitialMap(Conn conn, HashMap<String, ArrayList<HashMap<String, String>>> to_send: void
    + sendMap(Player player, HashMap<String, ArrayList<HashMap<String, String>>> to_send): void
    + formInitialMap(GameMap currentMap, List<String> colors): HashMap<String, ArrayList<HashMap<String, String>>>
    + formMap(List<Player> players): HashMap<String, ArrayList<HashMap<String, String>>>
}

class GameMap{
    - int numPlayer
    - ArrayList<String> ColorList
    - HashMap<String, List<Territory>> map
    + getTerritoryList(): ArrayList<Territory>
    + createBasicMap(): HashMap<String, List<Territory>>
    + createDukeMap(): HashMap<String, List<Territory>>
    + createTestMap(): HashMap<String, List<Territory>>
    + createTest3Map(): HashMap<String, List<Territory>>
    + getColorList(): ArrayList<String>
    + getMap(): HashMap<String, List<Territory>>
    + setMap(HashMap<String, List<Territory>> map): void
}

class Game {
    - List<Player> players
    - int numPlayer
    - List<String> colors
    - GameMap currentMap
    - List<Conn> allConnections
    - String gameState
    - int readyPlayer
    - int unitsPerPlayer 
    - int playerLowBound
    - int playerHighBound
    - int gameRound
    - MessageGenerator messageGenerator
    - HashMap<String, Supplier<HashMap<String, List<Territory>>>> mapCreateFns
    - String mapName
    - setupMapCreateFns(): void
    + gameFlow(Socket client_socket, int numClients): void
    + doActionPhase(Player p): void
    + doPlacementPhase(Socket client_socket, int numClients): Player
    + addPlayer(Player p): void
    + chooseColor(Conn conn): String
    + ifChooseWatch(Conn conn): String
    + chooseNumOfPlayers(Conn conn, int numClients): void
    + getNumPlayer(): int
    + initializeMap(int numOfPlayers): void
    + assignUnits(Player p, Conn conn): void
    + notifyAllPlayers(Conn conn, String newStage): void
    - promptTerritory(Player p, Conn conn): void
    - isValidTerritory(Player p, String territoryName, Conn conn): boolean
    - promptUnitNumber(Player p, String territoryName, Conn conn): void
    - isValidUnitNumber(Player p, String territoryName, String num, Conn conn): boolean
    + setNumPlayer(int num): void
    + doAction(Player p): boolean
    + makeActionOrder(Player p, String actionName): order
    + doOneAction(Player p, String actionName): void
    + checkNameReturnTerritory(String territory_name, GameMap map): Territory
    + worldwar(): HashMap<String, String>
    + generateUnit(): void
    + produceResources(): void
    + findWinner(): Player
}


class Player {
    - List<Territory> Territories
    - ArrayList<Unit> units
    - String color
    - Conn conn
    - int numUnits
    - AssignUnitRuleChecker placementchecker
    - boolean isWatch
    - int foodResource
    - int techResource
    - int techLevel
    - boolean hasResearched
    + unplacedUnits(): int
    + findNextUnplacedUnits(int amount): ArrayList<Unit>
    + placeUnitsSameTerritory(String t_name, int num): String
    + getTerritoryNames(): ArrayList<String>
    + addTerritories(List<Territory> territory): void
    + removeTerritory(Territory t): void
    + getTerritories(): List<Territory>
    + getColor(): String
    + getConn(): Conn
    + generateNewUnit(): void
    + checkLose(): boolean
    + setWatch(): void
    + getisWatch(): boolean
    + getFoodResource(): int
    + getTechResource(): int
    + removeFoodResource(int amount): void
    + removeTechResource(int amount): void
    + newResourcePerTurn(): void
    + displayResourceLevel(): String
    + updateTechLevel(): void
    + getTechLevel(): int
    + updateResearchRound(boolean status): void
    + getHasResearched(): boolean
}

class Conn {
    - Socket clientsocket
    - BufferedReader in
    - PrintWriter out
    + send(String msg): void
    + recv(): String
    + close(): void
}

class Unit {
    <<interface>>
    + setwhere(Territory where): void
    + getwhere(): Territory
    + getOwner(): Player
    + getAlive(): boolean
    + getisAttacker(): boolean
    + setisAttacker(): void
    + setDead(): void
    + getId(): void
}

class BasicUnit {
    - Player owner
    - Territory where
    - boolean isAttacker
    - int id
    - boolean isAlive
}



class Territory {
    - String name
    - List<Territory> neighbors
    - ArrayList<Unit> units
    - Player owner
    - Battle battle
    - HashMap<Territory, Integer> neighborDistance
    - int foodRate
    - int techRate
    + getUnits(): ArrayList<Unit>
    + existsBattle(): boolean
    + defendHome(): void
    + doBattle(): String
    + getAliveUnitsFor(Player player): ArrayList<Unit>
    + tryAddUnits(ArrayList<Unit> units): void
    + tryAddAttackers(ArrayList<Unit> units): void
    + tryRemoveUnits(int num, Player player): ArrayList<Unit>
    + getUnitsString(): String
    + getOwner(): Player
    + setOwner(Player owner): void
    + getName(): String
    + getNeighbors(): List<Territory>
    + getNeighborsNames(): String
    + territoryInfo(): String
    + setNeighbors(List<Territory> neighbors): void
    - addNeighbor(Territory neighbor): void
    + setFoodRate(int foodRate): void
    + setTechRate(int techRate): void
    + getFoodRate(): void
    + getTechRate(): void
    + setDistance(List<Territory> neighbors, List<Integer> distance): void
    + getDistanceMap(): HashMap<Territory, Integer>
    + getDistance(Territory neighbor): int
}


class Combat{
    - int seed
    - int diceNum
    + setSeed(int seed): void
    + setDiceNum(int diceNum): void
    + rolldice(): int
    + determineWin(Unit A, Unit B): Unit
}

class Battle{
    - ArrayList<ArrayList<Unit>> parties
    - Combat combat
    + resolveBattle(): Player
    + addGroup(ArrayList<Unit> units): void
    + setCombat(Combat combat): void
    + getParties(): ArrayList<ArrayList<Unit>>
    + checkGroupExisted(ArrayList<Unit> units): boolean
    + GameLog(): String
    + clearParty(): void
}

class Order {
    <<interface>>
    + tryAction(): String
}

class MoveOrder{
    - Territory from
    - Territory to 
    - int numUnits
    - Player player
    - GameMap gameMap
    + getPlayer(): Player
    + moveCost(int distance): int
}

class AttackOrder{
    - Territory from
    - Territory to 
    - int numUnits
    - Player player
    - GameMap gameMap
    + attackCost(): int
}

class ResearchOrder{
    - Player player
    + HashMap<Integer, Integer> researchCostTable
    - initializeTable(): void
}

class UpgradeOrder {
    - Player player
    - Territory belonging
    - int numUnits
    - int initialLevel
    - int upgradeAmount
}

class AssignUnitRuleChecker{
    + checkMyRule(String territory_name, Player player, int amount): String
}

class ResearchRuleChecker {
    + checkMyRule(Player player, int maxLevel): String
}

class UpgradeRuleChecker {
    + checkMyRule(): String
}

class OrderRuleChecker {
    - OrderRuleChecker next
    + checkMyRule(Territory from, Territory to, Player player, int numUnits, GameMap map): String
    + checkOrder(Territory from, Territory to, Player player, int numUnits, GameMap map): String
}

class MoveInputRuleChecker{
    + checkMyRule(): String
}

class MovePathRuleChecker{
    + checkMyRule(): String
    + dfs(Territory current, Territory destination, Player player, HashMap<String, List<Territory>> gameMap, HashSet<Territory> visited): boolean
    + dijkstraAlgorithm(Territory source, Territory destination, GameMap gameMap): int
    + getMinimumDistanceTerritory(Set<Territory> unsettledTerritories, Map<Territory, Integer> shortestDistances): Territory
    + relaxNeighbors(Territory territory, Map<Territory, Integer> shortestDistances, Set<Territory> unsettledTerritories): void
}

class AttackInputRuleChecker{
    + checkMyRule(): String
}

class AttackAdjacentRuleChecker{
    + checkMyRule(): String
}


```

