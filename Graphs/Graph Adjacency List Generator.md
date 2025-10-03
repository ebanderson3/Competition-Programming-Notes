```python
import random

def generate_graph(num_nodes, num_edges, directed=False, self_loops=False, multi_edges=False):
    """
    Generate a random graph as a list of edges.
    """
    edges = []
    edges_set = set()

    while len(edges) < num_edges:
        u = random.randint(1, num_nodes)  # 1-based for CP
        v = random.randint(1, num_nodes)

        if not self_loops and u == v:
            continue

        if not multi_edges:
            if directed:
                if (u, v) in edges_set:
                    continue
            else:
                if (u, v) in edges_set or (v, u) in edges_set:
                    continue

        edges_set.add((u, v))
        edges.append((u, v))

    return num_nodes, num_edges, edges


def print_graph(num_nodes, num_edges, edges):
    """
    Print graph in common competitive programming format.
    """
    print(num_nodes, num_edges)
    for u, v in edges:
        print(u, v)


# Example usage:
if __name__ == "__main__":
    n, m, edges = generate_graph(num_nodes=6, num_edges=8, directed=False, self_loops=False, multi_edges=False)
    print_graph(n, m, edges)

```