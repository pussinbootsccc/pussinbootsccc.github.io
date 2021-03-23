---
layout: page
title: Software Dependency
permalink: /algorithm/graph/softwaredependency
---
Given list of softwares and their dependencies, find the sequence of installation for a given software.

{% highlight txt %}
Input
A: B
B: D, F
D: E
E: F
X: E

Output
A => [F, E, D, B, A]
X => [F, E, X]
{% endhighlight %}

Key Ideas
- filter original graph to only retain related nodes with respect to given `software`
    - do `dfs` on given node, fill up its `visited` set
    - scan original graph to keep those nodes & edges appear in visited  
- run `topologicalSort` on filtered graph, to get sequence of all nodes in this graph  
    - build `succMap` : pred node => list of successors
    - update `indegree` map : succ node => its incoming edges, non-succ node with 0 indegree
    - check if all nodes in res, to decide if there is cycle dependencies   

Notes

{% highlight java %}
public class SoftwareDependency {
    public List<String> getSequence(Map<String, List<String>> rawInput, String software) {
        Map<String, List<String>> graph =  relatedGraph(rawInput, software);
        graph.putIfAbsent(software, new ArrayList<>()); // make sure given software is associated with predecessorList
        return topologicalSort(graph);
    }

    private List<String> topologicalSort(Map<String, List<String>> data) {
        Set<String> elements = new HashSet<>(); // check cycle
        Map<String, Integer> indegree = new HashMap<>();
        Map<String, List<String>> succMap = new HashMap<>();

        // build pred->succs map, called succMap
        for (Map.Entry<String, List<String>> entry : data.entrySet()) {
            String succ = entry.getKey(); // B : [C, D]
            List<String> preds = entry.getValue();
            elements.add(succ);
            for (String pred : preds) {
                elements.add(pred);
                succMap.putIfAbsent(pred, new ArrayList<>());
                succMap.get(pred).add(succ);
            }
            indegree.put(succ, preds.size()); // indegree = size of its preds list
        }

        Queue<String> queue = new ArrayDeque<>();
        for (String ele : elements) {
            if (indegree.getOrDefault(ele, 0) == 0) { // element with 0 indegree
                queue.offer(ele);
            }
        }

        List<String> res = new ArrayList<>();
        while (!queue.isEmpty()) {
            String curr = queue.poll();
            res.add(curr);

            if (succMap.containsKey(curr)) { // curr not have successors
                for (String succ : succMap.get(curr)) { // last element A will be called if not break ahead first
                    indegree.put(succ, indegree.get(succ) - 1);
                    if (indegree.get(succ) == 0) {
                        queue.offer(succ);
                    }
                }
            }
        }

        if (res.size() != elements.size()) // cannot find valid sequence for all software
            return new ArrayList<>();
        return res;
    }

    private Map<String, List<String>> relatedGraph(Map<String, List<String>> graph, String node) {
        Set<String> visited = new HashSet<>();
        visited.add(node);
        dfs(graph, node, visited); // fill up visited set with respect to input node

        // make new succ->pred graph to keep only related nodes with respect to entry node
        // those not in node's visited to be filtered out
        Map<String, List<String>> res = new HashMap<>();
        for (Map.Entry<String, List<String>> entry : graph.entrySet()) {
            String succ = entry.getKey(); // B : [C, D]
            List<String> preds = entry.getValue();
            if (visited.contains(succ)) {
                res.put(succ, new ArrayList<>());
                for (String pred : preds) {
                    if (visited.contains(pred)) {
                        res.get(succ).add(pred);
                    }
                }
            }
        }

        return res;
    }

    private void dfs(Map<String, List<String>> graph, String curr, Set<String> visited) {
        for (String neighbor : graph.getOrDefault(curr, new ArrayList<>())) {
            if (!visited.contains(neighbor)) {
                visited.add(neighbor);
                dfs(graph, neighbor, visited);
            }
        }
    }

    public static void main(String[] args) {
        Map<String, List<String>> input = new HashMap<>();

        input.put("A", new ArrayList<>());
        input.get("A").add("B");

        input.put("B", new ArrayList<>());
        input.get("B").add("D");
        input.get("B").add("F");

        input.put("D", new ArrayList<>());
        input.get("D").add("E");

        input.put("E", new ArrayList<>());
        input.get("E").add("F");

        input.put("X", new ArrayList<>());
        input.get("X").add("E");
        System.out.println(input);

        SoftwareDependency s = new SoftwareDependency();
        String software = "A";
        System.out.println("related graph: " + s.relatedGraph(input, software));
        System.out.println("final result: " + software + "=> " + s.getSequence(input, software));
    }
}
{% endhighlight %}

**Other Input**
{% highlight java %}
// input with cycle dependencies, no valid sequence exist
Map<String, List<String>> input = new HashMap<>();
input.put("A", new ArrayList<>());
input.get("A").add("B");

input.put("B", new ArrayList<>());
input.get("B").add("A");
{% endhighlight %}

