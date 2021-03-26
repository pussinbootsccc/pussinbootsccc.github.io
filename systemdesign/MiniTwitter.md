---
layout: page
title: Mini Twitter
permalink: /systemdesign/minitwitter
---
Implement a simple twitter. Support the following method:

{% highlight bash %}
postTweet(user_id, tweet_text). Post a tweet.
getTimeline(user_id). Get the given user's most recently 10 tweets posted by himself, order by timestamp from most recent to least recent.
getNewsFeed(user_id). Get the given user's most recently 10 tweets in his news feed (posted by his friends and himself). Order by timestamp from most recent to least recent.
follow(from_user_id, to_user_id). from_user_id followed to_user_id.
unfollow(from_user_id, to_user_id). from_user_id unfollowed to to_user_id.
{% endhighlight %} 


Key Ideas:
- maintain `friends` map to store `userID: set of userIDs`
- maintain `tweets`  map to store `userID: her list of tweet nodes`
- maintain a global `order` in twitter object to assign order value for tweets nodes created

{% highlight java %}
// this is main class need to design and implement
public class MiniTwitter {
    static class Node { // a wrapper class, to attach a timestamp field onto Tweet object
        public int order;
        public Tweet tweet;
        public Node(int o, Tweet t) {
            this.order = o;
            this.tweet = t;
        }
    }

    static class MyComparator implements Comparator<Node> {
        @Override
        public int compare(Node n1, Node n2) {
          return n1.order > n2.order ? -1 : 1; // timestamp larger first
        }
    }

    private Map<Integer, Set<Integer>> friends;
    private Map<Integer, List<Node>> tweets;
    private int order;
    public MiniTwitter() {
        this.friends = new HashMap<>(); // userID: set of userIDs
        this.tweets = new HashMap<>(); // userID: her list of tweet nodes
        this.order = 0; // a twitter service's instance field, to track a global order of tweets nodes
    }

    public Tweet postTweet(int userId, String text) {
        Tweet tweet = Tweet.create(userId, text);
        if (!tweets.containsKey(userId)) {
            tweets.put(userId, new ArrayList<>());
        }
        order++; // increase order value
        tweets.get(userId).add(new Node(order, tweet));
        return tweet;
    }

    public List<Tweet> getNewsFeed(int userId) {
        List<Node> tmp = new ArrayList<>();
        if (tweets.containsKey(userId))
            tmp.addAll(getLastTen(tweets.get(userId))); // add my 10 tweets

        if (friends.containsKey(userId)) {
            for (Integer friend : friends.get(userId)) {
                if (tweets.containsKey(friend))
                    tmp.addAll(getLastTen(tweets.get(friend))); // add all my friends' 10 tweets respectively
            }
        }

        Collections.sort(tmp, new MyComparator());
        List<Tweet> res = new ArrayList<>();
        tmp = getTopTen(tmp);
        for (Node node : tmp) {
            res.add(node.tweet);
        }
        return res;
    }

    public List<Tweet> getTimeline(int userId) { // one user's most recent 10
        List<Node> tmp = new ArrayList<>(); // can be empty
        if (tweets.containsKey(userId)) {
            tmp.addAll(getLastTen(tweets.get(userId))); // getLastTen already sorted from latest to oldest
        }

        List<Tweet> res = new ArrayList<>();
        for (Node node : tmp) {
            res.add(node.tweet);
        }
        return res;
    }

    public void follow(int fromUserId, int toUserId) {
        if (!friends.containsKey(fromUserId)) {
            friends.put(fromUserId, new HashSet<>());
        }
        friends.get(fromUserId).add(toUserId);
    }

    public void unfollow(int fromUserId, int toUserId) {
        if (friends.containsKey(fromUserId)) {
            friends.get(fromUserId).remove(toUserId);
        }
    }

    private List<Node> getLastTen(List<Node> nodes) { // latest tweet nodes by a user
        int k = Math.min(10, nodes.size());
        List<Node> res = new ArrayList();
        for (int i = nodes.size() - 1; i >= nodes.size() - k; i--) {
            res.add(nodes.get(i)); // extract last k nodes from end to middle
        }
        return res;
    }

    private List<Node> getTopTen(List<Node> nodes) {
        int k = Math.min(10, nodes.size());
        return nodes.subList(0, k);
    }
}

// this is a pre-defined class
// to have a autoId defined there is an ideal way
// assume autoId is not defined, so we need order field in MiniTwitter object
class Tweet {           // this class has a mixture of instance methods & static methods
    public int id;      // instance field
    public int userId; // instance field
    public String text; // instance field

    public static int autoId; // class field, globally auto increment id

    public Tweet(int userId, String text) {
        this.userId = userId;
        this.text = text;
        this.id = autoId;
    }

    public void update(String newTweetText) { // instance method：t.update()
        this.text = newTweetText;
    }

    public static Tweet create(int userId, String text) { // class method: Tweet.create()
        Tweet tweet = new Tweet(userId, text);
        Tweet.autoId += 1;
        return tweet;
    }
}
{% endhighlight %}