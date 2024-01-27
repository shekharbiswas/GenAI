# Vector DataBase

There are three main types of tools for vector search:
- Vector index libraries.
- Traditional databases.
- Vector databases.


All three types of tools support the basic features of indexing and querying vectors.
Vector databases are more full-featured, more scalable, and more performant.




![image](https://github.com/shekharbiswas/GenAI/assets/32758439/84f4532d-8071-45ca-bcf6-c4cbef00341d)




![image](https://github.com/shekharbiswas/GenAI/assets/32758439/4ad13cb0-5225-4e0f-a5b5-3f8aefb5c6f0)


![image](https://github.com/shekharbiswas/GenAI/assets/32758439/ae72789c-a5fc-440d-a16f-ae2489614c01)


![image](https://github.com/shekharbiswas/GenAI/assets/32758439/f86d8b2b-6f10-495b-80d6-faa2aaf7d9d2)


# Product Quantization

Product Quantization (PQ) is a technique used for vector compression. It is very effective in compressing high dimensional vectors for nearest-neighbor search, often compressing them by up to a factor of 64.


If you're a digital photographer, you may be familiar with JPEG compression which can compress raw images to be much smaller than their original size. JPEG is a lossy compression scheme — it loses a tiny bit of fidelity in the picture for a huge reduction in size, and is almost imperceptible to the naked eye. PQ is a lossy compression scheme for vectors, like JPEG is for images. And like JPEG, PQ saves a lot of space with very little loss in quality.


IVFPQ uses PQ to compress vectors, reducing the memory needed for the index and speeding up vector search times since not as much data must be examined to compare vectors.

# HNSW

The HNSW approach is a clever, fascinating form of vector similarity indexing that gives fast searches with good recall.

Before tackling the "hierarchical" nature of HNSW, let's look at just Navigable Small World (NSW) without the hierarchy. NSW is inspired by the mythical concept of “Six Degrees of Separation”: all people are six or fewer social connections away from each other, but at the same time each person typically maintains a relatively small number of friends. For this type of  graph, ANN can be solved with an efficient local search algorithm. Here's an example NSW graph:

![image](https://github.com/shekharbiswas/GenAI/assets/32758439/2e219364-46a1-4c94-a8d1-d3c7161bf845)

One approach to building this graph is to add nodes to the graph in random order, connecting each new node to its three nearest neighbors (or some other suitable small number). To query the graph, take the query vector, start at a random node and examine its neighbors. Go to the neighbor closest to the query vector. Repeat this process until you find the closest node to the query vector with no neighbors even closer.


You can repeat this process several times and return the K closest values to the query vector found, as the result of an approximate KNN search.


Now, let's add hierarchy to the NSW idea to create HNSW. You can build several NSW graphs stacked on each other, with the bottom layer (layer 0) having the full graph, and the next layer up having an NSW graph built from a random subset of the nodes in the layer below it and exponentially fewer nodes than the layer below it. Each node in a layer other than the bottom layer is connected directly to the same node in the layer below.


Here's an example three-layer HNSW structure:

![image](https://github.com/shekharbiswas/GenAI/assets/32758439/dd8b72c3-f09a-4de8-9068-b68f3910f3ce)


To search this, given a query vector q, you do an NSW search on the top layer. For the closest vector found in that layer, you drop down to the same node in the next layer and continue the search. This gets you continually closer to the "closest" nodes in the structure; the search on one layer puts you in the right neighborhood in the next layer down to make the search in that layer faster. You repeat this process until reaching the bottom layer.


Remember all the nodes visited. Repeat this some number of times proportional to k, then return the closest k vectors found.


The average search time on HNSW is proportional to log(number of vectors in the index) empirically. And, the index size is large and it needs to be in memory because of the random seek patterns on the graph.


Because HNSW takes a significant amount of RAM and time to build, consider that when deciding what kind of index to choose. Fortunately, there is a PQ-based variation of HNSW called HNSW_PQ that gives great performance and good recall with a lot less memory than straight HNSW; it has a small loss of recall due to the lossy compression of PQ.
