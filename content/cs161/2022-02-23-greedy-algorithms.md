# Greedy Algorithms

Idea:

* Make choices one-at-a-time
* Never look back
* Hope for the best

However: does not work everywhere.

## When to use greedy algorithms?

Problem should exhibit optimal substructure:

* Optimal solutions to a problem are made up from optimal solutions of subproblems
* Each problem depends on only one subproblem

## Common strategy for proving correctness

* Make a series of choices
    - Suppose we're on track to make optimal solution T*
* Show that at each step, choices won't rule out a globally optimal solution
    - Suppose that T* disagrees with the next greedy choice
* Manipulate T* in order to make a solution T that's not worse but agrees with the next greedy choice
    - Swap choice *k* instead of choice *j*
* After having made all choices, we haven't ruled out an optimal solution, so the solution we found is optimal

This can be turned into an inductive proof.

## Wrong greedy: unbounded knapsack

Idea: select items with best value/weight ratio

* This works if you can fill the entire knapsack capacity
* However, if there is space left over, then we have a suboptimal solution

## Correct greedy: activity selection

### Problem statement

Input:

* Activities a<sub>1</sub>...a<sub>n</sub>
* Start times s<sub>1</sub>...s<sub>n</sub>
* Finish times f<sub>1</sub>...f<sub>n</sub>

Output:

* Maximize the number of activities to do in a given day

### Greedy algorithm

* Pick activity you can add with the smallest finish time
* Repeat

What makes it greedy?

* At each step in the algorithm, make a choice:
    - Can increase activity set by one
    - Leaving room for future choices
    - Do this and hope for the best
* Hope that at the end of the day, this is a globally optimal solution

### Correctness

* When we make a choice, we don't rule out an optimal solution (i.e., there exists some optimal solution that contains that choice)
    - Suppose we have already chosen a<sub>i</sub> and there is still an optimal T* that extends our choices
    - Now consider the next choice a<sub>k</sub>
        - If a<sub>k</sub> in T*, we are done
        - If a<sub>k</sub> not in T*:
            - Let a<sub>j</sub> be the activity in T* with smallest finish  time after a<sub>i</sub>
            - Consider schedule T swapping a<sub>j</sub> for a<sub>k</sub>
            - T is still allowed, since a<sub>k</sub> has smaller ending time than a<sub>j</sub>, so does not conflict with anything
            - T still optimal, since it has the same numer of activities as T*
* The above is the inductive step of the proof of correctness.