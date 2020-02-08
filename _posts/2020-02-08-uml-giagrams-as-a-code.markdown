---
layout: post
title:  "How to organize UML diagrams as a code"
date:   2020-02-05 22:21:53 +0100
categories: jekyll update
---

Recently I was looking how to organize my UML sequence diagrams the most comfortable way. I wondering is it possible to organize it as a code. And I find a very useful tool titled PlantUML. This is a link to documentation [PlantUML][plant-uml]https://plantuml.com/en/ 

This is example how you can organise your diagram in code:

{% highlight yaml %}
@startuml

Alice -> Bob: Authentication Request
Bob --> Alice: Authentication Response

Alice -> Bob: Another authentication Request
Alice <-- Bob: Another authentication Response
@enduml
{% endhighlight %}

[plant-uml]: https://plantuml.com/en/ 
