# From Service to Platform: A Ranking System in Go

This talk was accepted for:

* GopherCon Europe 2022
* GopherCon UK 2022
* GoWest Conference 2022

## Elevator Pitch

What started out as an experimental service to rank livestreams evolved to a platform powering all type of social media content recommendations. Go’s stringent code philosophy paved the way to a modular pipeline-based system for scatter-gather workflows enabling anyone to add new ranking algorithms.

## Description

Pipedream, inspired by UNIX pipelines, is a ranking system which is used to define a series of different ranking pipelines by composing existing steps or adding new stages that perform ranking steps by making decisions based on gathering, sorting, filtering, and branching on data. We use it to rank social media content but its interface definition are sufficiently generic to rank any type of entities.

Go's interface type system and focus on writing simple and clear code helped us to create an API that optimizes for readers and contributors of the code. A single-method interface provides the backbone of the system, and is implemented by everything from stages to pipelines.

Part of the success which elevated Pipedream from an experimental service to a central platform in our architecture was the high level of performance, primarily as a result of the concurrency primitives and the Go runtime. Being able to fan-out requests in parallel and combine them later, fully utilizing multi-core machines allowed us to achieve low p9x latency while keeping high fault-tolerance a number one priority.

As technical lead on the project for over 3 years I want to share the lessons learned, what worked and  what did not work. It is meant to be an inspiration to the Go community to offer insights into patterns and abstractions that could be applied in other situations too.

### Outline

#### 1. What is a ranking/recommendation system? Why write one in Go? (6 min)

#### 2. Applying the UNIX philosopy to pipelines with gRPC (5 min)

UNIX pipelines were the inspiration for an  API allowing to create a graph of  acyclic stages to perform complex scatter-gather workflows. Review the gRPC protobuf definition and how it enforces an opionated Go design for individual stages implementation.

#### 3. Creating a toolbox through three principles (7 min)

Go's coding guidelines map well to the intent of how stages should be designed: they should do one thing and one thing only. Limiting the scope, clear naming and decoupling all contribute to creating a toolbox of reusable components for pipelines.

#### 4. Three pieces of middleware that delivered huge payoffs (8 min)

One which makes running the profiler obsolete, one which allows to shut down code without deployment mitigating incidents and one to log detailed diagnostic for individual requests

#### 5. Exposing the platform as a no-code abstraction (8 min)

Going from composing pipelines in Go to composing pipelines in YAML, dynamically during runtime and eventually moving the definitions outside the service boundaries. Platform-thinking starts by imaging the abstractions that are needed 6-12 months from now.

#### 6. Systematic improvements enabled by (and validating) our abstraction (8 min)

Discuss how the modular design helped to constantly think of optimizing performance and come up with caching strategies that ultimately contributed to the succcess but also reflect on some of the struggles peers faced with the more generic aspects of the implementation.
