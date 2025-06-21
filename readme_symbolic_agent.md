# A Reusable Symbolic Agent for Interpretable Planning and Lifelong Generalization

This project implements a symbolic agent that can:

- Learn from failed actions by identifying missing symbolic preconditions
- Log learned symbolic knowledge for reuse across tasks
- Generalize across new planning tasks using previously learned facts
- Track predicate usage for interpretability and pruning

It is inspired by recent advances in continual learning and interpretable planning, offering advantages over latent-only baselines like IsCiL, ICPAD, and NeSyC.

---

## Completed Tasks (Task 1–4)

| Task | Description                           | Success After Learning | Notes                       |
| ---- | ------------------------------------- | ---------------------- | --------------------------- |
| 1    | Pick and grasp a cup                  | S                     | Requires `clear(cup)`       |
| 2    | Place the cup on the shelf            | S                    | Requires `reachable(shelf)` |
| 3    | Place bottle in box                   | S                    | Requires `open(box)`        |
| 4    | Place cup in box (zero-shot general.) | S                      | Reused `open(box)` from T3  |

---

## Symbolic Learning Log

After a failed plan execution, the system attempts to infer a missing symbolic precondition and appends it to a persistent symbolic log. Logged predicates are automatically reused in future tasks via:

```python
initial_state = augment_with_learned_predicates(initial_state)
```

Example of a learned predicate being logged:

```python
logger.log_learned_predicate(Predicate("open", ["box"]))
```

---

## Predicate Usage Tracking

To support ablation and pruning, the system tracks the number of times each predicate is used during planning.

```python
# Predicate Usage Report Example:
🔸 Predicate: holding — Used 6 time(s)
🔸 Predicate: open — Used 2 time(s)
...
```

Low-usage predicates are flagged for possible pruning.

---

## Evaluation Plan

### Environments

- **VirtualHome / ALFWorld** for long-horizon planning
- **Meta-World / RLBench** for forward/backward transfer

### Baselines

- IsCiL, ICPAD, NeSyC (latent-only)
- NSCL/PRL (visual-symbolic hybrids)

### Metrics

| Category          | Metric                                | LaSER Advantage                           |
| ----------------- | ------------------------------------- | ----------------------------------------- |
| Planning          | Task Completion Rate                  | Explicit rule composition                 |
| Lifelong Learning | Forward/Backward Transfer, Forgetting | Rules persist, low forgetting             |
| Domain Generaliz. | Domain Update Count                   | Reusable symbolic log, adaptive operators |
| Interpretability  | Human Alignment on Plan Trace         | Full symbolic plans with traceable rules  |

---

## Sample Code Snippet

Define an operator and plan:

```python
# Operator
place_op = Operator(
    name="place_cup_on_shelf",
    parameters=["robot", "cup", "shelf"],
    preconds=[Predicate("holding", ["robot", "cup"]), Predicate("reachable", ["shelf"])],
    effects=[Predicate("on", ["cup", "shelf"])]
)

# Initial and goal states
initial_state = set([Predicate("holding", ["robot", "cup"]), Predicate("reachable", ["shelf"])])
goal_state = set([Predicate("on", ["cup", "shelf"])])

# Plan
steps = plan(initial_state, goal_state, operators)
```

---

## Next Step

Evaluate against baselines (IsCiL, NeSyC, ICPAD) in common simulated planning environments.

---

## File Structure

```
SymbolicPlanner.v1.ipynb      # Main notebook
/mnt/data/LaSER_Symbolic_Log  # Log directory for learned predicates
```

Let me know if you'd like this README exported as a `.md` file!

