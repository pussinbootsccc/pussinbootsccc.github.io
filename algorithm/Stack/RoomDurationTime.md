---
layout: page
title: Room Duration Time
permalink: /algorithm/stack/roomdurationtime
---
To compute the accumulated stay duration in each loaction

{% highlight txt %} 
Input:
Name Timestamp Action
lobby 0 enter
hallway 10 enter
room1 20 enter
room1 40 exit // go back to the hallway
room1 50 enter
room1 60 exit // go back to the hallway
hallway 80 exit // go back to the lobby
lobby 100 exit
lobby 120 enter
room2 140 enter
room2 150 exit // go back to the lobby
lobby 155 exit

Output:
lobby=55, hallway=40, room1=30, room2=10
{% endhighlight %}

Key Ideas
- maintain an on-the-fly variable `enterTime`, for entering stack-top room   
- maintain an stack to keep active rooms names, the top is curr standing room name  
- compute `duration` for curr room in each round, if curr room exist (stack not empty). 


{% highlight java %}
public class RoomDuration {
    static class Node {
        String name;
        int time;
        String action;
        Node(String name, int time, String action) {
            this.name = name;
            this.time = time;
            this.action = action;
        }
    }

    public Map<String, Integer> getDuration(List<Node> input) {
        Deque<String> stack = new ArrayDeque<>(); // active room's name, top is curr standing room
        Map<String, Integer> map = new HashMap<>(); // roomName : accumulated time

        int enterTime = 0;
        for (Node toDo : input) {
            if (!stack.isEmpty()) { // curr standing room exist
                String curr = stack.peekFirst(); // stack top: curr standing room
                int duration = toDo.time - enterTime; // duration in curr room
                map.put(curr, map.get(curr) + duration); // update curr room's acc duration
            }
            // to enter a room, put into stack
            if (toDo.action.equals("enter")) {
                map.putIfAbsent(toDo.name, 0);
                stack.offerFirst(toDo.name);
            }
            // to exist room, to pop stack top
            if (toDo.action.equals("exit")) {
                stack.pollFirst();
            }
            // still a room on top of stack, capture enterTime for that room
            if (!stack.isEmpty()) {
                enterTime = toDo.time;
            }
        }
        return map;
    }

    public static void main(String[] args) {
        Object[][] rawInput = {
                {"lobby", 0,  "enter"},
                {"hallway", 10, "enter"},
                {"room1", 20, "enter"},
                {"room1", 40, "exit"},
                {"room1", 50,  "enter"},
                {"room1", 60, "exit"},
                {"hallway", 80, "exit"},
                {"lobby", 100, "exit"},
                {"lobby", 120,  "enter"},
                {"room2", 140,  "enter"},
                {"room2", 150,  "exit"},
                {"lobby", 155, "exit"} 
            };

        List<Node> input = new ArrayList<>();
        for (Object[] row : rawInput) {
            input.add(new Node((String)row[0], (int)row[1], (String)row[2]));
        }

        RoomDuration s = new RoomDuration();
        System.out.println(s.getDuration(input));
    }
}
{% endhighlight %}
