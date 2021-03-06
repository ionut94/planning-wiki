---
layout: page
title: PDDL 2.1
has_children: true
parent: Reference
permalink: /ref/pddl21
nav_order: 1
---
# PDDL 2.1

Contributors: {% git_author %}

## Introduction

PDDL 2.1 was the language of the 2002 AIPS Competition and built upon the syntax defined for [PDDL 1.2](../pddl). In the 2002 Competition, planners were set the challenge of considering more complicated domains and problems which feature both temporal and numeric considerations (scheduling and resources). As a result, additions the language were necessary to facilitate modelling time and numbers.

This guide will only show features that have been changed or added to PDDL 1.2 in order to form 2.1

## Domain And Problem File

### Domain

```cl
(define (domain rover-domain)
    (:requirements :durative-actions :fluents :duration-inequalities)
    (:types rover waypoint)
    (:functions
        (battery-amount ?r - rover)
        (sample-amount ?r - rover)
        (recharge-rate ?r - rover)
        (battery-capacity)
        (sample-capacity)
        (distance-travelled)
    )
    (:predicates
        ... ; Predicates omitted
	)

    (:durative-action move
        :parameters
            (?r - rover
             ?fromwp - waypoint
             ?towp - waypoint)

        :duration
            (= ?duration 5)

        :condition
	        (and
	            (at start (rover ?rover))
	            (at start (waypoint ?from-waypoint))
	            (at start (waypoint ?to-waypoint))
	            (over all (can-move ?from-waypoint ?to-waypoint))
	            (at start (at ?rover ?from-waypoint))
	            (at start (> (battery-amount ?rover) 8)))

        :effect
	        (and
	            (at end (at ?rover ?to-waypoint))
	            (at end (been-at ?rover ?to-waypoint))
	            (at start (not (at ?rover ?from-waypoint)))
	            (at start (decrease (battery-amount ?rover) 8))
                (at end (increase (distance-travelled) 5))
                )
	)
    ... ; Additional actions omitted
)
```

### Problem

```cl
(define
    (problem rover1)
    (:domain rover-domain)
    (:objects
        r1 r2 - rover
        wp1 wp2 - waypoint
    )
    (:init
        (= (battery-amount r1) 100)
        (= (recharge-rate r1) 2.5)
        ...
    )
    (:goal (and
            ...
        )
    )
    (:metric maximize (+
            (battery-amount r1)
            (battery-amount r2)
        )
    )
)
```

## References

- [PDDL - The Planning Domain Definition Language](http://www.cs.cmu.edu/~mmv/planning/readings/98aips-PDDL.pdf), [Ghallab, M. Howe, A. Knoblock, C. McDermott, D. Ram, A. Veloso, M. Weld, D. Wilkins, D.]
- [PDDL2.1: An Extension to PDDL for Expressing Temporal Planning Domains](https://jair.org/index.php/jair/article/view/10352/24759), [Fox, M. Long, D.]
- [PDDL Examples](https://github.com/yarox/pddl-examples)
- [OPTIC - Optimising Preferences and Time Dependent Costs](https://nms.kcl.ac.uk/planning/software/optic.html)