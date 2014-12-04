﻿- title : F#, this ain't a game
- description : Using F# and it's ecosystem in production in a game
- author : Andrea Magnorsky
- theme : solarized
- transition : default

***
<div style=".state-background {
    background-image: images/fsharp_logo.png;
} ">

## F#, this aint a game

### Andrea Magnorsky

Digital Furnace Games ␣ ▀ ␣ BatCat Games ␣ ▀ ␣ GameCraft Foundation

- @SilverSpoon 
- [roundcrisis.com](roundcrisis.com)


---

#### Working on OniKira: Demon Killer 

Available on Steam Early Access




<iframe width="853" height="480" src="//www.youtube.com/embed/8OH31zfRlDs?rel=0" frameborder="0" allowfullscreen></iframe>

---

#### Working on OniKira: Demon Killer 

Available on Steam Early Access

![](images/onikira.jpg)

---

***

### Why?

![tar pit](images/tarpit.jpg)

[Out of the tar pit - Mosley, Marks 2006](https://github.com/papers-we-love/papers-we-love/blob/master/design/out-of-the-tar-pit.pdf)

<small>pic src http://en.wikipedia.org/wiki/McKittrick_Tar_Pits </small>

---

### Complexity causes

* State
* Code volume
* Flow of control

---


***

### F# 

<img src="images/fsharp_logo.png" alt="fs" style="width: 250px;"/>

* Functional first
* .net Interop
* Concise
* Type system
* OSS
* Divine learning curve

---
### 
<iframe width="1024" height="768" src="//fsharp.org" frameborder="0" allowfullscreen></iframe>

---


    // one-liners
    [1..100] |> List.sum |> printfn "sum=%d"

    // no curly braces, semicolons or parentheses
    let square x = x * x
    let sq = square 42 

    // simple types in one line
    type Person = {First:string; Last:string}

    // complex types in a few lines
    type Employee = 
      | Worker of Person
      | Manager of Employee list

    // type inference
    let jdoe = {First="John";Last="Doe"}
    let worker = Worker jdoe

Visit **F# for Fun and Profit** for more examples

---
#### What we use

![pattern-matching](images/pm.png)

---

    match msg with                
    | :? ActorDiedMessage as actorMessage ->                      
        match actorMessage.GameObject.GetComponent<CharacterController>() with 
        | null -> ()
        | characterController -> 
            if ( characterController.IsOnGround() = false ) then 
                PlatformHelper.UnlockAchievement GameAchivement.AirKill
                this.GameObj.GetComponent<ScriptComponent>().DisposeLater()
    | _ -> ()

---

### Active Patterns

They are active because reasons

---

    let (|LeftKey|RightKey|OtherKey|) (keyboard:KeyboardInput) = 
        if keyboard.KeyPressed(Key.Left) then LeftKey
        elif keyboard.KeyPressed(Key.Right) then RightKey
        else OtherKey "Hi, you pressed a key...well that is interesting :D"

    interface ICmpUpdatable with
        member this.OnUpdate()=        
            match DualityApp.Keyboard with            
            | LeftKey  -> playerGo Left
            | RightKey-> playerGo Right
            | OtherKey s-> ()

---

    let (|SpaceKey|) (keyboard:KeyboardInput) = 
        keyboard.KeyPressed(Key.Space)

    let (|Hold100ms|) (keyboard:KeyboardInput) = 
        keyboard.KeyPressedFor(Key.I, 100)  

    match DualityApp.Keyboard width        
    | SpaceKey true & Hold100ms false -> playerGo Jump
    | SpaceKey true & Hold100ms true -> playerGo DoubleJump

---
***
### REPL

#### Now: exploration
#### Future: Live coding

***
### Ecosystem: What we use now

* FsCheck
* Fake
* Compiler Services

---

### Future

* Ferop 
* Paket           

---
***
### Property Testing with FsCheck

>Why write tests, when you can generate them

---
### FsCheck


* QuickCheck [paper](http://www.cs.tufts.edu/~nr/cs257/archive/john-hughes/quick.pdf) by Koen Claessen and John Hughes 
* Superb article by Scot Wlashin on [Property based testing ](http://fsharpforfunandprofit.com/posts/property-based-testing/) as part of the F# advent calendar.
* Can be used from C# 
* Small library
* Can run stand alone or integrates with NUnit and xUnit

---

### What is a property?

> x + x = 2 * x 

or

> List.rev(List.rev list) = list

---


    [<Property>]
    let ``When adding x to x then result is double x``(x:int)=
        x + x = 2*x

---

    let preconditionMaxHealth maxHealth = maxHealth > 0

    [<Property(Verbose = true)>]    
    let ``Health should never be higher than max`` (x:int)(maxHealth:int)=        
        let healthComponent = initedHealth
        healthComponent.MaxHealth <- maxHealth
        healthComponent.IncreaseHealth x
        preconditionMaxHealth maxHealth ==>  (healthComponent.MaxHealth >= healthComponent.Health)

---

<img src="images/fscheck.png" alt="fs" style="width: 950px;"/>

---
***
#### Fake

* It's mature.
* Builds for .net and mono, it's cross platform. 
* No need to know F# to use it.
* Integrates with CI Server.

---
### Hello world!

    // include Fake lib
    #r @"tools\FAKE\tools\FakeLib.dll"
    open Fake 
    Target "Foo" (fun _ ->
        trace "Hello World from Foo"
    )
    
    Target "Bar" (fun _ -> 
        trace "Hello World from Bar"
    )
    "Bar"
      ==> "Foo"
    
    RunTargetOrDefault "Foo"

---
### Real world

---
***
### The party piece
##

##Compiler.Services
    
---
## 

* File watcher
* Code in any editor :)
* Compiler Services call to fsc
* Future -> live coding

---

<img src="images/fcs.png" alt="fs" style="width: 950px;"/>


***

#### Interop

---

#### C# consuming F# code

Use namespaces in F# or prefix with global::YourModuleName

    [lang=cs]
    using System;
    class Program
    {
        static void Main(string[] args)
        {
            var s = Calculator.Calc.add("4 4", "+");
            Console.WriteLine("The sum is {0}", s);
        }
    }
and the F# side

    namespace Calculator
    module Calc =

        open System
        let add numbers delimiter =    
            // Do stuff to add numbers            



---

#### F# consuming C# code

    module MathTest =

    open NUnit.Framework

    let [<Test>] ``2 + 2 should equal 4``() =
        Assert.AreEqual(2 + 2, 4)



---
***


**Like games?** Dublin GameCraft on the 6th of December @ Microsoft

F# and games workshop from 10am to noon.

<img src="images/gamecraft-logo.png" alt="GC" style="width: 200px;"/>


**Functional Kats** Monthly meetup 
![FK](images/fk.jpeg)  


***

### Thanks :D

![onikira](images/onikira.jpg)

- @SilverSpoon 
- [roundcrisis.com](roundcrisis.com)

</div>