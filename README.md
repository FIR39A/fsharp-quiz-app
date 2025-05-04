# fsharp-quiz-app

# QuizApp Projekt – Fájlok

Az alábbiakban megtalálod a `QuizApp` teljes fájlszerkezetét és a fájlok tartalmát. Másold be a megfelelő helyre a fájlokat.

```
QuizApp/
├── public/
│   ├── index.html
│   └── style.css
├── src/
│   ├── QuizApp.fsproj
│   ├── Quiz.fs
│   ├── Model.fs
│   ├── Msg.fs
│   ├── Update.fs
│   ├── View.fs
│   └── Program.fs
├── package.json
└── webpack.config.js
```

---

## public/index.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8" />
    <title>F# Quiz App with Timer & Leaderboard</title>
    <link rel="stylesheet" href="style.css" />
</head>
<body>
    <div id="root"></div>
    <script src="bundle.js"></script>
</body>
</html>
```

## public/style.css

```css
body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 20px;
    background-color: #f4f4f4;
}
#root {
    max-width: 600px;
    margin: auto;
    background: #fff;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 2px 8px rgba(0,0,0,0.1);
}
button {
    margin-top: 10px;
}
.skip-button {
    background-color: #f39c12;
    color: white;
    border: none;
    padding: 8px;
    cursor: pointer;
    margin-left: 10px;
}
.skip-button:disabled {
    background-color: #ccc;
    cursor: default;
}
.timer {
    font-weight: bold;
    margin-bottom: 10px;
}
.leaderboard {
    margin-top: 20px;
    border-top: 1px solid #ddd;
    padding-top: 10px;
}
.leaderboard ul { list-style: none; padding: 0; }
.leaderboard li { margin-bottom: 4px; }
```

## src/QuizApp.fsproj

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>net7.0</TargetFramework>
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Fable.Core" Version="4.5.0" />
    <PackageReference Include="Fable.Browser.Dom" Version="2.18.1" />
    <PackageReference Include="Fable.Elmish" Version="4.3.0" />
    <PackageReference Include="Fable.Elmish.React" Version="4.0.0" />
    <PackageReference Include="Fable.React" Version="9.4.0" />
  </ItemGroup>
</Project>
```

## src/Quiz.fs

```fsharp
module Quiz

open System

type Question = {
    Text: string
    Options: string list
    AnswerIndex: int
}

type Difficulty =
    | Easy
    | Medium
    | Hard

let questionBank: (Difficulty * Question) list = [
    // Easy questions (10)
    Easy, { Text = "2 + 2 = ?"; Options = ["3"; "4"; "5"]; AnswerIndex = 1 }
    Easy, { Text = "Capital of France?"; Options = ["Paris"; "Rome"; "Berlin"]; AnswerIndex = 0 }
    Easy, { Text = "5 - 3 = ?"; Options = ["1"; "2"; "3"]; AnswerIndex = 1 }
    Easy, { Text = "Color of the sky?"; Options = ["Blue"; "Green"; "Red"]; AnswerIndex = 0 }
    Easy, { Text = "1 + 1 = ?"; Options = ["2"; "3"; "4"]; AnswerIndex = 0 }
    Easy, { Text = "Square of 2?"; Options = ["2"; "4"; "6"]; AnswerIndex = 1 }
    Easy, { Text = "3 - 2 = ?"; Options = ["1"; "2"; "0"]; AnswerIndex = 0 }
    Easy, { Text = "Sun rises in the?"; Options = ["East"; "West"; "North"]; AnswerIndex = 0 }
    Easy, { Text = "10 / 2 = ?"; Options = ["4"; "5"; "6"]; AnswerIndex = 1 }
    Easy, { Text = "6 + 3 = ?"; Options = ["8"; "9"; "10"]; AnswerIndex = 1 }

    // Medium questions (10)
    Medium, { Text = "3 * 7 = ?"; Options = ["21"; "24"; "18"]; AnswerIndex = 0 }
    Medium, { Text = "Square root of 49?"; Options = ["6"; "7"; "8"]; AnswerIndex = 1 }
    Medium, { Text = "12 / 3 = ?"; Options = ["4"; "3"; "5"]; AnswerIndex = 0 }
    Medium, { Text = "5^2 = ?"; Options = ["10"; "25"; "20"]; AnswerIndex = 1 }
    Medium, { Text = "9 * 2 = ?"; Options = ["18"; "16"; "20"]; AnswerIndex = 0 }
    Medium, { Text = "7 + 8 = ?"; Options = ["14"; "15"; "16"]; AnswerIndex = 1 }
    Medium, { Text = "8 - 3 = ?"; Options = ["4"; "5"; "6"]; AnswerIndex = 1 }
    Medium, { Text = "11 / 11 = ?"; Options = ["0"; "1"; "11"]; AnswerIndex = 1 }
    Medium, { Text = "4^2 = ?"; Options = ["8"; "12"; "16"]; AnswerIndex = 2 }
    Medium, { Text = "6 * 6 = ?"; Options = ["36"; "30"; "42"]; AnswerIndex = 0 }

    // Hard questions (10)
    Hard, { Text = "12! / (10! * 2!) = ?"; Options = ["66"; "72"; "56"]; AnswerIndex = 0 }
    Hard, { Text = "Integral of x dx = ?"; Options = ["x"; "x^2/2 + C"; "ln x"]; AnswerIndex = 1 }
    Hard, { Text = "Derivative of x^2 = ?"; Options = ["2x"; "x"; "x^2"]; AnswerIndex = 0 }
    Hard, { Text = "Limit of (1+1/n)^n as n→∞?"; Options = ["e"; "1"; "2"]; AnswerIndex = 0 }
    Hard, { Text = "cos(0) = ?"; Options = ["0"; "1"; "-1"]; AnswerIndex = 1 }
    Hard, { Text = "log10(100) = ?"; Options = ["1"; "2"; "3"]; AnswerIndex = 1 }
    Hard, { Text = "5! = ?"; Options = ["120"; "24"; "60"]; AnswerIndex = 0 }
    Hard, { Text = "√(16) = ?"; Options = ["2"; "4"; "8"]; AnswerIndex = 1 }
    Hard, { Text = "30/5 = ?"; Options = ["5"; "6"; "8"]; AnswerIndex = 1 }
    Hard, { Text = "2^5 = ?"; Options = ["32"; "16"; "64"]; AnswerIndex = 0 }
]

let shuffle rnd xs = xs |> List.map (fun x -> rnd.Next(), x) |> List.sortBy fst |> List.map snd
let pickQuestions difficulty count = let rnd = Random() in questionBank |> List.filter (fst >> ((=) difficulty)) |> List.map snd |> shuffle rnd |> List.take count
```

## src/Model.fs

```fsharp
module Model

open Quiz

let questionTime = 15

type State = {
    Level: int
    Questions: Question list
    CurrentIndex: int
    Score: int
    Finished: bool
    SkipUsed: bool
    TimeLeft: int
    PlayerName: string
}

type Model =
    | NotStarted
    | InProgress of State
    | Completed of int
    | ShowLeaderboard of (string * int) list

let initModel = NotStarted
```

## src/Msg.fs

```fsharp
module Msg

type Msg =
    | StartQuiz
    | Answer of int
    | NextStep
    | Restart
    | Skip
    | Tick
    | EnterName of string
    | SubmitScore
    | ViewLeaderboard
```

## src/Update.fs

```fsharp
module Update

open Quiz
open Model
open Msg

let leaderboard = ref []

let questionsForLevel level =
    match level with 1 -> Easy | 2 -> Medium | _ -> Hard
    |> pickQuestions 10

let initState lvl = {
    Level = lvl
    Questions = questionsForLevel lvl
    CurrentIndex = 0
    Score = 0
    Finished = false
    SkipUsed = false
    TimeLeft = questionTime
    PlayerName = ""
}

let update msg model =
    match model, msg with
    | NotStarted, StartQuiz -> InProgress (initState 1)
    | InProgress st, EnterName name -> InProgress { st with PlayerName = name }
    | InProgress st, Tick when not st.Finished ->
        let tl = st.TimeLeft - 1
        if tl <= 0 then
            let idx = st.CurrentIndex + 1
            let fin = idx >= List.length st.Questions
            InProgress { st with CurrentIndex = idx; Finished = fin; TimeLeft = questionTime }
        else InProgress { st with TimeLeft = tl }
    | InProgress st, Skip when not st.Finished && not st.SkipUsed ->
        let idx = st.CurrentIndex + 1
        let fin = idx >= List.length st.Questions
        InProgress { st with CurrentIndex = idx; Finished = fin; SkipUsed = true; TimeLeft = questionTime }
    | InProgress st, Answer i when not st.Finished ->
        let q = st.Questions.[st.CurrentIndex]
        let sc = if i = q.AnswerIndex then st.Score + 1 else st.Score
        let idx = st.CurrentIndex + 1
        let fin = idx >= List.length st.Questions
        InProgress { st with CurrentIndex = idx; Score = sc; Finished = fin; TimeLeft = questionTime }
    | InProgress st, NextStep when st.Finished && st.Level < 3 -> InProgress (initState (st.Level + 1))
    | InProgress st, NextStep when st.Finished -> Completed st.Score
    | Completed score, SubmitScore ->
        leaderboard := (!leaderboard) @ [ (match model with InProgress st -> st.PlayerName | _ -> ""), score ]
        ShowLeaderboard !leaderboard
    | ShowLeaderboard _, ViewLeaderboard -> NotStarted
    | Completed _, Restart
    | ShowLeaderboard _, Restart -> NotStarted
    | _ -> model
```

## src/View\.fs

```fsharp
module View

open Fable.React
open Fable.React.Props
open Model
open Msg

let questionView q dispatch =
    div [] [
        h3 [] [ str q.Text ]
        ul [] (q.Options |> List.mapi (fun i o -> li [] [ button [ OnClick(fun _ -> dispatch (Answer i)) ] [ str o ] ]))
    ]

let view model dispatch =
    match model with
    | NotStarted ->
        div [] [
            h2 [] [ str "Enter your name:" ]
            input [ Type "text"; Placeholder "Name"; OnChange(fun ev -> dispatch (EnterName ev.Value)) ] []
            button [ OnClick(fun _ -> dispatch StartQuiz) ] [ str "Start Quiz" ]
        ]
    | InProgress st when not st.Finished ->
        div [] [
            div [ ClassName "timer" ] [ str (sprintf "Time left: %d" st.TimeLeft) ]
            h2 [] [ str (sprintf "Level %d - Q %d/%d" st.Level (st.CurrentIndex + 1) (List.length st.Questions)) ]
            questionView st.Questions.[st.CurrentIndex] dispatch
            p [] [ str (sprintf "Score: %d" st.Score) ]
            button [ ClassName "skip-button"; OnClick(fun _ -> dispatch Skip); Disabled st.SkipUsed ] [ str (if st.SkipUsed then "Skip used" else "Skip Question") ]
        ]
    | Completed score ->
        div [] [
            h2 [] [ str (sprintf "%s, you scored %d!" (match model with InProgress st -> st.PlayerName | _ -> "Player") score) ]
            button [ OnClick(fun _ -> dispatch SubmitScore) ] [ str "Submit Score" ]
        ]
    | ShowLeaderboard entries ->
        div [] [
            h2 [] [ str "Leaderboard" ]
            div [ ClassName "leaderboard" ] [
                ul [] (entries |> List.map (fun (n,s) -> li [] [ str (sprintf "%s: %d" n s) ]))
            ]
            button [ OnClick(fun _ -> dispatch Restart) ] [ str "Play Again" ]
        ]
```

## src/Program.fs

```fsharp
module Program

open Elmish
open Elmish.React
open Fable.Core.JsInterop
open Browser.Dom
open Model
open Update
open View

let timerSub dispatch =
    let id = window.setInterval((fun _ -> dispatch Tick), 1000.0)
    { new System.IDisposable with member _.Dispose() = window.clearInterval(id) }

Program.mkProgram (fun () -> initModel, Cmd.none) update view
|> Program.withReact "root"
|> Program.withSubscription (fun model -> if matches<|> model<|> InProgress then Cmd.ofSub timerSub else Cmd.none)
|> Program.run
```

## package.json

```json
{
  "name": "fsharp-quiz-app",
  "version": "1.0.0",
  "scripts": {
    "start": "dotnet fable watch src/QuizApp.fsproj --outDir public --run \"webpack serve --mode development\"",
    "build": "dotnet fable src/QuizApp.fsproj --outDir public && webpack --mode production"
  },
  "devDependencies": {
    "webpack": "^5",
    "webpack-cli": "^4",
    "webpack-dev-server": "^4",
    "css-loader": "^6",
    "style-loader": "^3"
  },
  "dependencies": {
    "react": "^17",
    "react-dom": "^17"
  }
}
```

## webpack.config.js

```js
const path = require('path');
module.exports = {
  entry: './public/Program.js',
  output: { filename: 'bundle.js', path: path.resolve(__dirname, 'public') },
  devServer: { static: './public', port: 8080 },
  module: { rules: [ { test: /\.css$/, use: ['style-loader','css-loader'] } ] },
  resolve: { extensions: ['.js'] }
};
```

---

### Használat

1. Másold a fenti fájlokat a megfelelő helyekre.
2. A projekt gyökerében futtasd:

```bash
 dotnet tool restore
 dotnet restore src/QuizApp.fsproj
 npm install
 npm start
```

3. Nyisd meg: `http://localhost:8080` és élvezd a kvízt timerrel, skip-gombbal és leaderboard-dal!")
