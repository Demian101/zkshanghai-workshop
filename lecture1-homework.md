# 第1课 课后作业


## Exercise 1: 

Q: Currently, you can only select adjacent pairs of nodes to check. Would the proof still be zero knowledge if you could pick arbitrary pairs of nodes to check?

Answer: Although checking two non-adjacent pairs of nodes does not aid in verifying the 3-coloring proof, the verifier can still select adjacent pairs of nodes to achieve verification of the 3-coloring map proof. If the verifier could pick non-adjacent pairs of nodes to check, even though the displayed colorings are mixed up, the verifier might choose a pair of nodes that have the same color (after mixing up, the same color node will still be displayed as the same color). In that case, the verifier may be able to deduce the solution by identifying which nodes have the same color.


## Exercise 2: 

Q: The equation currently being used for confidence is 1-(1/E)^n, where E is the number of edges in the graph, and n is the number of trials run. Is this the correct equation? Why is there no prior?

A: Yes. Because the prover can change colors between different rounds of the game, so the event of picking an edge each time is independent.
