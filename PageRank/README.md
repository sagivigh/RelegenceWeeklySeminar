
# Mining of Massive Datasets - PageRank


    import networkx as nx

## Example:


    G = nx.DiGraph()
    G.add_edge('F', 'B')
    G.add_edge('F', 'E')
    G.add_edge('E', 'B')
    G.add_edge('E', 'D')
    G.add_edge('E', 'F')
    G.add_edge('D', 'A')
    G.add_edge('D', 'B')
    G.add_edge('C', 'B')
    G.add_edge('B', 'C')
    G.add_edge('G1', 'F')
    G.add_edge('G2', 'F')
    G.add_edge('G3', 'E')
    G.add_edge('G4', 'E')
    G.add_edge('G5', 'E')
    G.add_edge('G3', 'B')
    G.add_edge('G4', 'B')
    G.add_edge('G5', 'B')
    
    ranks = nx.pagerank(G, max_iter = 200)
    ranks = {id:100*r for (id,r) in ranks.items()}
    ranks




    {'A': 3.024679028033108,
     'B': 38.78023770752343,
     'C': 34.56056397843077,
     'D': 3.3583940394729526,
     'E': 6.215408746644173,
     'F': 6.073908693610029,
     'G1': 1.597361561257104,
     'G2': 1.597361561257104,
     'G3': 1.597361561257104,
     'G4': 1.597361561257104,
     'G5': 1.597361561257104}



## Solving the flow equations:


    G = nx.DiGraph()
    G.add_edge('y', 'y')
    G.add_edge('a', 'y')
    G.add_edge('y', 'a')
    G.add_edge('m', 'a')
    G.add_edge('a', 'm')
    
    ranks = nx.pagerank(G, max_iter = 200)
    ranks




    {'a': 0.39879457405360386, 'm': 0.21948769557545744, 'y': 0.3817177303709387}




    a = np.array([[0.5,0,-0.5], [0.5,-1,0], [1,1,1]])
    b = np.array([0,0,1])
    x = np.linalg.solve(a, b)
    x





    array([ 0.4,  0.2,  0.4])



## Naive power Iteration implementation


    def first_attempt(G, r0=None, it=100, verbose=False):
        out_degree = G.out_degree()
    
        ri = r0 if r0 != None else {name:1.0/len(G) for name in G}
        ranks = ri.copy()
        for _ in xrange(it):
            for name in G:
                ri[name] = sum([ranks[p]/out_degree[p] for p in G.predecessors(name)])
            ranks = ri.copy()
            if verbose:
                print ranks
        return ranks


    G = nx.DiGraph()
    G.add_edge('y', 'y')
    G.add_edge('a', 'y')
    G.add_edge('y', 'a')
    G.add_edge('m', 'a')
    G.add_edge('a', 'm')
    
    ranks = first_attempt(G)
    ranks




    {'a': 0.39999999993259971, 'm': 0.20000000004165552, 'y': 0.40000000002574443}



## Spider trap


    G = nx.DiGraph()
    G.add_edge('a', 'b')
    G.add_edge('b', 'a')
    
    ranks = first_attempt(G, r0={'a':1, 'b':0}, it=5, verbose=True)
    ranks

    {'a': 0, 'b': 1}
    {'a': 1, 'b': 0}
    {'a': 0, 'b': 1}
    {'a': 1, 'b': 0}
    {'a': 0, 'b': 1}





    {'a': 0, 'b': 1}



## Dead end


    G = nx.DiGraph()
    G.add_edge('a', 'b')
    
    ranks = first_attempt(G, r0={'a':1, 'b':0}, it=5, verbose=True)
    ranks

    {'a': 0.0, 'b': 1}
    {'a': 0.0, 'b': 0.0}
    {'a': 0.0, 'b': 0.0}
    {'a': 0.0, 'b': 0.0}
    {'a': 0.0, 'b': 0.0}





    {'a': 0.0, 'b': 0.0}



## Spider trap 2


    G = nx.DiGraph()
    G.add_edge('y', 'y')
    G.add_edge('a', 'y')
    G.add_edge('y', 'a')
    G.add_edge('a', 'm')
    G.add_edge('m', 'm')
    
    ranks = first_attempt(G, it=50)
    ranks




    {'a': 6.0292544509602878e-06,
     'm': 0.9999842152069206,
     'y': 9.755538628475332e-06}




    ranks = nx.pagerank(G, max_iter=50)
    ranks




    {'a': 0.1267828884553442, 'm': 0.6925514946957173, 'y': 0.1806656168489385}



## The complete algorithm


    def pagerank(G, r0=None, beta=0.85, it=100, verbose=False):
        N = len(G)
        out_degree = G.out_degree()
    
        ranks = ri = r0 if r0 != None else {name:1./N for name in G}
        for _ in xrange(it):
            for name in G:
                ri[name] = beta*sum([ranks[p]/out_degree[p] for p in G.predecessors(name)])
            S = sum([r for r in ri.values()])
            ranks = {name:r+(1.-S)/N for name,r in ri.items()}
            if verbose:
                print ranks
        return ranks


    ranks = pagerank(G, it=50)
    ranks




    {'a': 0.12678288546406888, 'm': 0.69255150252697784, 'y': 0.18066561200895331}




    nx.pagerank??


    


    
