---
layout: page
title: Pick-up And Deliver Orders
permalink: /algorithm/dfs/pickupdeliverorders
---
**Validation**
check if the given input is a valid sequence.  
  
{% highlight bash %}
valid:   "P12P2D12D2", "P1P29D29D1", "P1D1P2D2", "P2P1D1D2", "P2P1D2D1", "P2D2P1D1"
invalid: "P1D1P2D1", "P1D2P2D1", "P2P2D2D2", "P2D1P1D2", "P1D1P1D1"
{% endhighlight %}

{% highlight java %}
public class ValidPickupAndDeliveryOrders {
    public List<String> findAllValid(String[] input) {
        List<String> res = new ArrayList<>();
        for (String s : input) {
            if (valid(s)) {
                res.add(s);
            }
        }
        return res;
    }

    private boolean valid(String s) {
        Set<Integer> pickSet = new HashSet<>();
        Set<Integer> deliverSet = new HashSet<>();

        int i = 0;
        int n = s.length();
        while (i < s.length()) {
            if (s.charAt(i) == 'P') { // pick-up case: put id into set
                i++; // points to digit
                int pickID = 0;
                while (i < n && Character.isDigit(s.charAt(i))) {
                    pickID = pickID * 10 + (s.charAt(i) - '0');
                    i++;
                }
                if (pickSet.contains(pickID))
                    return false;
                pickSet.add(pickID);

            } else if (s.charAt(i) == 'D') { // delivery case: delete id from set
                i++; // points to digit
                int deliveryID = 0;
                while (i < n && Character.isDigit(s.charAt(i))) {
                    deliveryID = deliveryID * 10 + (s.charAt(i) - '0');
                    i++;
                }
                if (!pickSet.contains(deliveryID) || deliverSet.contains(deliveryID))
                    return false; // pick-up set not hold delivery id or delivery id repeated
                deliverSet.add(deliveryID);
            }
        }
        return pickSet.size() == deliverSet.size(); // finish dispatch all orders
    }
}
{% endhighlight %}

**Generation**

print out all valid sequences given n orders.  
{% highlight bash %}
n = 2
output = [P1, P2, D1, D2], [P1, P2, D2, D1], [P1, D1, P2, D2], [P2, P1, D1, D2], [P2, P1, D2, D1], [P2, D2, P1, D1]
{% endhighlight %}

{% highlight java %}
public class ValidPickupAndDeliveryOrdersII {
    List<List<String>> generateAll(int n) {
        List<List<String>> res = new ArrayList<>();
        boolean[] pick = new boolean[n]; // such id has been picked
        boolean[] deliver = new boolean[n]; // such id has been delivered
        helper(pick, deliver, new ArrayList<>(), res, n);
        return res;
    }

    private void helper(boolean[] pick, boolean[] deliver, List<String> path, List<List<String>> res, int n) {
        if (path.size() == n * 2) {
            res.add(new ArrayList<>(path)); // res.add(String.join("", path)) can make a concat String
            return;
        }
        // choose P case
        for (int i = 0; i < n; i++) { // orderID
            if (!pick[i]) {
                pick[i] = true;
                path.add("P" + (i + 1));
                helper(pick, deliver, path, res, n);
                pick[i] = false; // reset
                path.remove(path.size() - 1); // reset
            }
        }
        // choose D case
        for (int i = 0; i < n; i++) {
            if (pick[i] && !deliver[i]) {
                deliver[i] = true;
                path.add("D" + (i + 1));
                helper(pick, deliver, path, res, n);
                deliver[i] = false; // reset
                path.remove(path.size() - 1); // reset
            }
        }
    }
}
{% endhighlight %}


