---
layout: page
title: Same Kind Transactions
permalink: /algorithm/map/samekindtransactions
---
Get sum up amount for the same kind of transactions

{% highlight txt %}
INPUT:
{10, 1, "APPROVED", 10.1},
{11, 1, "APPROVED", 1.9},
{20, 1, "SETTLED", 22.2},
{30, 2, "SETTLED", 30.5},
{31, 2, "SETTLED", 1.5},
{40, 2, "APPROVED", 100.0}

OUTPUT:
Result{amount=32.0, list=[Transaction{id=30, amount=30.5}, Transaction{id=31, amount=1.5}]}
Result{amount=22.2, list=[Transaction{id=20, amount=22.2}]}
Result{amount=12.0, list=[Transaction{id=10, amount=10.1}, Transaction{id=11, amount=1.9}]}
Result{amount=100.0, list=[Transaction{id=40, amount=100.0}]}
{% endhighlight %}

{% highlight java %}
class Transaction {
    public final static int APPROVED = 1;
    public final static int SETTLED = 2;

    public int id;
    public int parentId;
    public String status;
    public double amount;

    public Transaction(int id, int parentId, String status, double amount) {
        this.id = id;
        this.parentId = parentId;
        this.status = status;
        this.amount = amount;
    }
    @Override
    // define the same parentId & status will be the same type
    public boolean equals(Object obj) {
        if (this == obj) {
            return true;
        }
        if (!(obj instanceof Transaction)) {
            return false;
        }
        Transaction that = (Transaction) obj;
        return this.parentId == that.parentId && this.status.equals(that.status);
    }

    @Override
    // define the same parentId & status will be the same type
    public int hashCode() {
        int res = 17;
        res = 31 * res + Integer.hashCode(parentId);
        res = 31 * res + status.hashCode();
        return res;
    }

    @Override
    public String toString() {
        return "Transaction{" +
                "id=" + id +
                ", amount=" + amount +
                '}';
    }
}

class Result {
    double amount;
    List<Transaction> list;

    public Result(List<Transaction> ts, double amount) {
        this.list = ts;
        this.amount = amount;
    }

    @Override
    public String toString() {
        return "Result{" +
                "amount=" + amount + ", " +
                "list=" + list +
                '}';
    }
}

// MAIN LOGIC
public class SameKindTransaction {
    public Map<Transaction, Result> group(List<Transaction> transactions) {
        Map<Transaction, Result> map = new HashMap<>();

        for (Transaction t : transactions) {
            map.putIfAbsent(t, new Result(new ArrayList<>(), 0.0)); // a new t : new Result()
            map.get(t).list.add(t);
            map.get(t).amount += t.amount;
        }
        return map;
    }

    public static void main(String[] args) {
        Object[][] input = {
                {10, 1, "APPROVED", 10.1},
                {11, 1, "APPROVED", 1.9},
                {20, 1, "SETTLED", 22.2},
                {30, 2, "SETTLED", 30.5},
                {31, 2, "SETTLED", 1.5},
                {40, 2, "APPROVED", 100.0}};
        List<Transaction> transactions = new ArrayList<>();
        for (Object[] o : input) {
            transactions.add(new Transaction((int)o[0], (int)o[1], (String)o[2], (double)o[3]));
        }
        SameKindTransaction s = new SameKindTransaction();
        Map<Transaction, Result> res = s.group(transactions);
        // print out how each aggregated result looks like
        for (Transaction key : res.keySet()) {
            System.out.println(res.get(key));
        }
    }
}
{% endhighlight %}

**Short-Cut Implementation**
{% highlight java %}

    public Map<String, Double> group(List<Transaction> transactions) {
        Map<String, Double> map = new HashMap<>();

        for (Transaction t : transactions) {
            // concate cared fields to make a key
            String key = String.valueOf(t.parentId) + "," + String.valueOf(t.status);
            map.putIfAbsent(key, 0.0);
            map.put(key, map.get(key) + t.amount);
        }
        return map;
    }

{% endhighlight %}

