Single Responsibility Principle
===============================
The Single Responsibility Principle states that any class, module or method should have only one reason to change. Instead of thinking of a responsibility with the more traditional definition, we should instead consider a responsibility as a *reason for change*. 

For example, let's consider a Tic Tac Toe game with a ```Board``` module:

```
defmodule Board do
  
  def generate_blank_board do 
    #generate a board state
  end
  
  def display_current_board do 
    #displays the board to the ui
  end
  
  def update_board do
    #updated the board's state
  end
end
```

At first glance, this module may seem okay; each of the three functions is related specifically to a board. However, if we think about the *reasons for change* we may see that both ```generate_blank_board``` and ```update_board``` are specifically dealing with the **state** of the board itself. While, ```display_current_board``` deals with **displaying** the board to the user. So, it is technically violating the Single Responsibility Principle. 

Leaving these two separate responsibilities in the ```Board``` module, would essentially be coupling the idea of a board’s state with it's display. If we ever want to change how a board's **state is stored** or how a board is **displayed**, we would be dealing with two very different responsibilities.  Say our requirements change, and now instead of displaying our game on the command line, we also want to display the it in the browser as well. While creating a browser UI, there would be a great chance that we could introduce a bug into the "state keeping" section of our module since they are coupled within the same module. Furthermore, there is also a chance that this one change could break another part of our game system. In essence, this is a fragile system. A good rule of thumb to use, while creating software (and making sure to adhere to the Single Responsibility Principle) is to say outloud what your class, module or method is doing. If we end up using the words "and" or "or", our class is responsible for more than once thing. In this case our 	```Board``` is keeping a board’s state **and** displaying the board.


Another important implication of the Single Responsibility Principle is the idea of code reusability. Let's say that we are creating a new application that had use for a module that was only concerned with *displaying* a game board. In order to use ```Board``` as it currently is designed, we would also have to include all the logic involved in generating and updating the board's state, even though this particular application doesn't care about those functions. This would include a lot of unused code, and would force our new application to retest and redeploy our game system any time there was a change to how the ```Board``` state was updated - though that isn't even used in this new application. 

So, how do we fix the ```Board``` module above? Instead of including code to both **display** a board and **keep it's state**, let's split those pieces into two separate modules:
```
defmodule BoardState do 
 def generate_blank_board do 
    #generate a board state
  end
  
  def update_board do
    #updated the board's state
  end
end
```
```
defmodule BoardPresenter do 
  def display_current_board do 
    #displays the board to the ui
  end
end
```

It's a simple change, and now we have two separate modules that can take care of very different aspects of a Board. In the scenario about, where an application may need to only display a board, we can use the ```BoardPresenter```. And when we need to keep a board's state, clearly we can use ```BoardState```. Most likely, these two modules will be used in conjunction with each other - when a board's state is updated using ```BoardState.update_board```, we could potentially pass it's result into ```BoardPresenter.display_current_board```. By making this small change, our ```Board``` now adheres to the Single Responsibility, and we’ve also ensured that our software is more flexible and reusable.
