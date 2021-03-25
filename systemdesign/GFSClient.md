---
layout: page
title: GFS Client
permalink: /systemdesign/gfsclient
---
Implement a simple client for GFS (Google File System, a distributed file system), it provides the following methods:

{% highlight bash %}
read(filename). Read the file with given filename from GFS.
write(filename, content). Write a file with given filename & content to GFS.

There are two private methods that already implemented in the BaseGFSClient class:
readChunk(filename, chunkIndex). Read a chunk from GFS.
writeChunk(filename, chunkIndex, chunkData). Write a chunk to GFS.
{% endhighlight %} 

Key Ideas
- keep `chunkCount` map to lookup `filename : its number of chunks` (then you know its chunk ids)  
- write sub-content chunk by chunk by chunk id  
- read sub-content chunk by chunk via chunk id, then concat the whole content 

{% highlight java %}
class BaseGFSClient {
    private Map<String, String> chunkList;
    public BaseGFSClient() {}
    public String readChunk(String filename, int chunkIndex) {
        // Fetch the content given filename and chunkIndex
        return null;
    }
    public void writeChunk(String filename, int chunkIndex, String content) {
      // Write a chunk to GFS
    }
}

public class GFSClient extends BaseGFSClient {
    private Map<String, Integer> chunkCount;
    private int chunkSize;

    public GFSClient(int chunkSize) {
        this.chunkSize = chunkSize;
        this.chunkCount = new HashMap<>(); // filename : count of file's chunks
    }

    public String read(String filename) {
        if (!chunkCount.containsKey(filename))
            return null;
        StringBuilder content = new StringBuilder();
        for (int i = 0; i < chunkCount.get(filename); i++) {
            String chunk = readChunk(filename, i); // read a chunk
            content.append(chunk);
        }
        return content.toString();
    }

    public void write(String filename, String content) {
        int n = content.length();
        int count = ((n - 1) / chunkSize) + 1; // number of chunks for this file
        chunkCount.put(filename, count); // update map

        for (int i = 0; i < count; i++) { // chunk id
            int start = i * chunkSize; // start index
            int end = (i != count - 1 ? start + chunkSize : n); // end index
            String chunk = content.substring(start, end); // write this chunk content
            writeChunk(filename, i, chunk);
        }
    }
}


{% endhighlight %}