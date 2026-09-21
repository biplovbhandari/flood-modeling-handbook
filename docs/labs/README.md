# Lab Conventions

The labs turn handbook concepts into inspectable calculations, evidence traces, artifact checks, diagnoses, and design reviews.
They are learning exercises rather than operational acceptance tests.

## Prerequisites

Complete the chapters named in each lab before starting the exercise.
Read [Source Authority](../reference/source-authority.md) and use its five evidence labels when the distinction affects a claim.
Use [Glossary](../reference/glossary.md) and [Equations and Units](../reference/equations-and-units.md) for stable terms, variables, units, and assumptions.
Every required input is contained in the prompt, linked to a public scientific source, or supplied elsewhere in this handbook.

## Evidence labels

Use the labels for the roles defined in [Source Authority](../reference/source-authority.md).

- **Scientific foundation** identifies an established concept supported by an authoritative public source.
- **Applied example** identifies constructed teaching material and its stated assumptions.
- **Design principle** identifies a reusable engineering or scientific-software rule.
- **Evidence note** states what a supplied calculation, observation, record, or check can and cannot establish.
- **Open question** records a gap, conflict, or decision that the supplied evidence does not resolve.

Do not treat a label as a confidence score.
Use more than one label when different parts of a conclusion depend on different evidence roles.

## Core work and optional tools

All required work can be completed by reading and calculating from the prompt and its handbook or public-source links.
Optional calculation tools may be used to check arithmetic, but their output does not replace units, assumptions, or reasoning.
A successful calculation or readable artifact does not by itself establish numerical adequacy, hydraulic validity, library completeness, or readiness.

## Answer discipline

State assumptions before using them.
Carry units through every calculation and check dimensional consistency.
Identify the spatial, temporal, probability, and datum context of each value when those references affect meaning.
Separate supplied facts, calculations, inferences, evidence limits, and open questions.
When evidence conflicts, identify the conflict and state what would resolve it.
When a required input is absent, preserve the limitation instead of inventing a value.

## Synthetic data and safety boundaries

Treat every reach, scenario, record, value, and evidence packet identified as synthetic as constructed teaching material.
Do not infer that a synthetic result describes an external system, operational threshold, accepted method, or real location.
Do not use secrets, credentials, personal data, sensitive values, or unapproved operational data in lab work.
Do not use a lab prompt as authorization to run a solver, modify infrastructure, write to a database, publish artifacts, or change an external system.

## Prompts and solutions

Each prompt is a standalone file under `labs/`.
Each solution is a separate file under `labs/solutions/` so that the learner can attempt the exercise before reading the worked reasoning.
Attempt every requested table, calculation, classification, and verdict before opening the solution.
Solutions show one supported reasoning path and preserve the limits of the supplied evidence.
They do not prove that another dataset, model, or intended use will produce the same conclusion.
