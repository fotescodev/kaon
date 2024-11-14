---
hidden: true
---

# Architecture View

> A Sequence diagram is an interaction diagram that shows how processes operate with one another and in what order.

Mermaid can render sequence diagrams.

```mermaid
flowchart RL
 subgraph s3[" "]
    direction TB
        BTC["Bitcoin"]
        CL["Consensus<br> Layer"]
  end
 subgraph s4[" "]
    direction TB
        NI["Node Interface"]
  end
 subgraph s1["BFT Consensus:"]
    direction TB
        n4["Crosschain Mempool"]
        s2["s2"]
        n1["Verifies data accuracy<br> and integrity"]
  end
 subgraph s2["sMPC Groups / TSS Providers"]
    direction TB
        n3["Witness Group"]
        n2["Validator Group"]
  end
    s3 ~~~ s4
    s4 ~~~ s1
    n2 ~~~ n3
    n1 ~~~ s2
    CL -- Kaon Transaction --> NI
    BTC -- Kaon Transaction --> NI
    NI -- Mirrored Transaction --> CL & BTC
    CL -- Rotate and determine new signers for new epoch --> s1
    NI -- Kaon Transaction --> s1
    s1 -- Aggregates and broadcasts<br> Mirrored Transactions to Kaon<br> or Bitcoin --> NI
    n4 -- Pulls in transaction<br> to be mirrored* --> s2
    s2 -- Pushes partially<br> signed mirrored<br> transaction --> n4
    s1 ~~~ t1["*mirrored transactions can be mirrored again"]
    CL ~~~ t2["Validator group: First required signature to send transaction to Kaon or to Bitcoin Network"] & t3["Witness Group: Second required signature, approve locked BTC to be sent or mark it as corrupt"] & t4["Transactions are processed in a similar manner as on Bitcoin"]
     BTC:::Peach
     t1:::Ash
     t2:::Ash
     t3:::Ash
     t4:::Ash
    classDef Ash stroke-width:1px, stroke-dasharray:none, stroke:#999999, fill:#EEEEEE, color:#000000, font-family:Arial, font-size:14px
    classDef Peach stroke-width:1px, stroke-dasharray:none, stroke:#FBB35A, fill:#FFEFDB, color:#8F632D
    classDef Class_01 stroke-width:4px, stroke-dasharray: 0, stroke:#FF6D00, fill:#FF6D00
    style BTC fill:#FF6D00,stroke:#000000,font-family:Arial,font-size:14px,color:#FFD600,stroke-width:1px,stroke-dasharray: 0
    style CL fill:transparent,stroke:#000000,font-family:Arial,font-size:14px
    style NI fill:none,stroke:#000000,font-family:Arial,font-size:14px
    style n4 fill:none,stroke:#000000,font-family:Arial,font-size:14px
    style s2 fill:none,stroke:#000000,font-family:Arial,font-size:14px
    style n1 fill:transparent,stroke:#000000,font-family:Arial,font-size:14px,stroke-width:0px
    style n3 fill:none,stroke:#000000,font-family:Arial,font-size:14px
    style n2 fill:none,stroke:#000000,font-family:Arial,font-size:14px
    style s3 fill:transparent,stroke-width:0px,font-family:Arial
    style s4 fill:none,stroke-width:0px,font-family:Arial
    style s1 fill:none,stroke:#000000,font-family:Arial,font-size:14px
```

## Syntax

### Participants

The participants can be defined implicitly as in the first example on this page. The participants or actors are rendered in order of appearance in the diagram source text. Sometimes you might want to show the participants in a different order than how they appear in the first message. It is possible to specify the actor's order of appearance by doing the following:

```mermaid
sequenceDiagram
    participant John
    participant Alice
    Alice->>John: Hello John, how are you?
    John-->>Alice: Great!
```

### Aliases

The actor can have a convenient identifier and a descriptive label.

```mermaid
sequenceDiagram
    participant A as Alice
    participant J as John
    A->>J: Hello John, how are you?
    J->>A: Great!
```

## Messages

Messages can be of two displayed either solid or with a dotted line.

```
[Actor][Arrow][Actor]:Message text
```

There are six types of arrows currently supported:

| Type | Description                                 |
| ---- | ------------------------------------------- |
| ->   | Solid line without arrow                    |
| -->  | Dotted line without arrow                   |
| ->>  | Solid line with arrowhead                   |
| -->> | Dotted line with arrowhead                  |
| -x   | Solid line with a cross at the end (async)  |
| --x  | Dotted line with a cross at the end (async) |

## Activations

It is possible to activate and deactivate an actor. (de)activation can be dedicated declarations:

```mermaid
sequenceDiagram
    Alice->>John: Hello John, how are you?
    activate John
    John-->>Alice: Great!
    deactivate John
```

There is also a shortcut notation by appending `+`/`-` suffix to the message arrow:

```mermaid
sequenceDiagram
    Alice->>+John: Hello John, how are you?
    John-->>-Alice: Great!
```

Activations can be stacked for same actor:

```mermaid
sequenceDiagram
    Alice->>+John: Hello John, how are you?
    Alice->>+John: John, can you hear me?
    John-->>-Alice: Hi Alice, I can hear you!
    John-->>-Alice: I feel great!
```

## Notes

It is possible to add notes to a sequence diagram. This is done by the notation Note \[ right of | left of | over ] \[Actor]: Text in note content

See the example below:

```mermaid
sequenceDiagram
    participant John
    Note right of John: Text in note
```

It is also possible to create notes spanning two participants:

```mermaid
sequenceDiagram
    Alice->John: Hello John, how are you?
    Note over Alice,John: A typical interaction
```

## Loops

It is possible to express loops in a sequence diagram. This is done by the notation

```
loop Loop text
... statements ...
end
```

See the example below:

```mermaid
sequenceDiagram
    Alice->John: Hello John, how are you?
    loop Every minute
        John-->Alice: Great!
    end
```

## Alt

It is possible to express alternative paths in a sequence diagram. This is done by the notation

```
alt Describing text
... statements ...
else
... statements ...
end
```

or if there is sequence that is optional (if without else).

```
opt Describing text
... statements ...
end
```

See the example below:

```mermaid
sequenceDiagram
    Alice->>Bob: Hello Bob, how are you?
    alt is sick
        Bob->>Alice: Not so good :(
    else is well
        Bob->>Alice: Feeling fresh like a daisy
    end
    opt Extra response
        Bob->>Alice: Thanks for asking
    end
```

## Styling

Styling of the a sequence diagram is done by defining a number of css classes. During rendering these classes are extracted from the

### Classes used

| Class        | Description                                                 |
| ------------ | ----------------------------------------------------------- |
| actor        | Style for the actor box at the top of the diagram.          |
| text.actor   | Styles for text in the actor box at the top of the diagram. |
| actor-line   | The vertical line for an actor.                             |
| messageLine0 | Styles for the solid message line.                          |
| messageLine1 | Styles for the dotted message line.                         |
| messageText  | Defines styles for the text on the message arrows.          |
| labelBox     | Defines styles label to left in a loop.                     |
| labelText    | Styles for the text in label for loops.                     |
| loopText     | Styles for the text in the loop box.                        |
| loopLine     | Defines styles for the lines in the loop box.               |
| note         | Styles for the note box.                                    |
| noteText     | Styles for the text on in the note boxes.                   |

### Sample stylesheet

```css
body {
    background: white;
}

.actor {
    stroke: #CCCCFF;
    fill: #ECECFF;
}
text.actor {
    fill:black;
    stroke:none;
    font-family: Helvetica;
}

.actor-line {
    stroke:grey;
}

.messageLine0 {
    stroke-width:1.5;
    stroke-dasharray: "2 2";
    marker-end:"url(#arrowhead)";
    stroke:black;
}

.messageLine1 {
    stroke-width:1.5;
    stroke-dasharray: "2 2";
    stroke:black;
}

#arrowhead {
    fill:black;

}

.messageText {
    fill:black;
    stroke:none;
    font-family: 'trebuchet ms', verdana, arial;
    font-size:14px;
}

.labelBox {
    stroke: #CCCCFF;
    fill: #ECECFF;
}

.labelText {
    fill:black;
    stroke:none;
    font-family: 'trebuchet ms', verdana, arial;
}

.loopText {
    fill:black;
    stroke:none;
    font-family: 'trebuchet ms', verdana, arial;
}

.loopLine {
    stroke-width:2;
    stroke-dasharray: "2 2";
    marker-end:"url(#arrowhead)";
    stroke: #CCCCFF;
}

.note {
    stroke: #decc93;
    stroke: #CCCCFF;
    fill: #fff5ad;
}

.noteText {
    fill:black;
    stroke:none;
    font-family: 'trebuchet ms', verdana, arial;
    font-size:14px;
}
```

## Configuration

Is it possible to adjust the margins for rendering the sequence diagram.

This is done by defining `mermaid.sequenceConfig` or by the CLI to use a json file with the configuration. How to use the CLI is described in the [mermaidCLI](mermaidCLI.html) page. `mermaid.sequenceConfig` can be set to a JSON string with config parameters or the corresponding object.

```javascript
mermaid.sequenceConfig = {
    diagramMarginX:50,
    diagramMarginY:10,
    boxTextMargin:5,
    noteMargin:10,
    messageMargin:35,
    mirrorActors:true
};
```

### Possible configuration params:

| Param           | Description                                                                                                                                 | Default value |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------- | ------------- |
| mirrorActor     | Turns on/off the rendering of actors below the diagram as well as above it                                                                  | false         |
| bottomMarginAdj | Adjusts how far down the graph ended. Wide borders styles with css could generate unwantewd clipping which is why this config param exists. | 1             |
