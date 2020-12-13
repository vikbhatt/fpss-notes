# Case Study: The Water Pouring Problem

For the sake of example, say you have two glasses of size 4 liters, and 9 liters. You need to produce given quantity (say 6 liters) in one of the glasses. There is no marking on glasses. Moves available to you are, either you can fill a glass completely with a faucet, or you can pour from one glass to another until the glass which you pour is empty or the glass you are pouring into are full.
Let's generalize the problem. We have `n` number of glasses, each with arbitrary capacity

We are going to represent

- Glass: `Int` (ranging from 0 to n-1)
- State: `Vector[Int]`, for each glass the number of deciliters of water in the glass.
- Moves:
  - `Empty(glass)`
  - `Fill(glass)`
  - `Pour(from, to)`

The idea would be to generate all move sequences (call them `Paths`) until we hit a path where one glass contains right amount of liquid in the target glass.

```scala
class Pouring(capacity: Vector[Int]){
    //States
    type State = Vector[Int]
    val initialState = capacity map (x => 0)

    // Moves
    trait Move {
        def change(state: State): State 
    }
    case class Empty(glass: Int) extends Move {
        def change(state: State) = state updated (glass, 0)
    }
    case class Fill(glass: Int) extends Move{
        def change(state: State) = state updated (glass, capacity(glass))
    }
    case class Pour(from: Int, to: Int) extends Move{
        def change(state: State) =  {
            val amount = state(from) min (capacity(to) - state(to))
            state updated (from, state(from)-amount) updated (to, state(to)+amount)
        }
    }

    val glasses = 0 until capacity.length
    val moves = (for(g <- glasses ) yield Empty(g) ++
                for(g <- glasses) yield  Fill(g) ++
                for(from <- glasses; to <- glasses if from !=to) yield Pour(from, to))

    //Paths
    class Path(history: List[Move]){
        def endState: state
    }
}
```
