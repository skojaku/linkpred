# Link Prediction Dataset

This is a Python class that generates a link prediction dataset for evaluating link prediction models. It can be used to create train-test splits of a given network and sample negative edges for evaluation.

## Requirements

Make sure you have the following Python packages installed:

- `numpy`
- `pandas`
- `scipy`
- `tqdm`

## Install 

```bash
pip install git+https://github.com/skojaku/linkpred.git
```

## Usage

```python
from linkpred.LinkPredictionDataset import LinkPredictionDataset

model = LinkPredictionDataset(testEdgeFraction=0.5, negative_edge_sampler="uniform")
model.fit(net)
train_net, target_edge_table = model.transform()
```

## Class: LinkPredictionDataset

### Initializer

```python
LinkPredictionDataset(
    testEdgeFraction,
    negative_edge_sampler,
    negatives_per_positive=1,
    conditionedOnSource=False,
    all_negatives=False
)
```

- `testEdgeFraction` (float): Fraction of edges to be removed from the given network for testing.
- `negative_edge_sampler` (str): Type of negative edge sampler. Can be "uniform" for conventional link prediction evaluation or "degreeBiased" for degree-biased sampling.
- `negatives_per_positive` (int, optional): Number of negative edges to sample per positive edge. Defaults to 1.
- `conditionedOnSource` (bool, optional): Whether to condition negative edge sampling on the source node of the positive edge. Defaults to False.
- `all_negatives` (bool, optional): If True, evaluate all positives and all negatives. Defaults to False.

### Methods

#### `fit(net)`

Fit the link prediction dataset to a given network.

- `net` (numpy array or scipy sparse matrix): The input network for which the link prediction dataset will be generated.

#### `transform()`

Transform the fitted dataset to obtain the train network and the target edge table.

Returns:
- `train_net` (scipy sparse matrix): The train network after removing the test edges.
- `target_edge_table` (pandas DataFrame): DataFrame containing information about the test and negative edges.

## Class: TrainTestEdgeSplitter

This class is used internally by the `LinkPredictionDataset` class to split the edges into train and test sets.

### Initializer

```python
TrainTestEdgeSplitter(fraction=0.5)
```

- `fraction` (float): Fraction of edges that will be removed for testing.

### Methods

#### `fit(A)`

Split train and test edges using the Minimum Spanning Tree (MST) method.

- `A` (scipy sparse matrix): The input network.

#### `transform()`

Returns the train and test edges obtained after fitting the splitter.

Returns:
- `train_edges_` (tuple of arrays): Tuple containing arrays of source and target nodes for train edges.
- `test_edges_` (tuple of arrays): Tuple containing arrays of source and target nodes for test edges.

--- 
# Negative Edge Sampler

### Example

```python
from linkpred.LinkPredictionDataset import NegativeEdgeSampler

sampler = NegativeEdgeSampler(negative_edge_sampler = "uniform")
sampler.fit(net)
src, trg = sampler.sampling(size = 100)
```

### Initializer

```python
NegativeEdgeSampler(negative_edge_sampler)
```

- `negative_edge_sampler` (str): Type of negative edge sampler. Can be "uniform" for conventional link prediction evaluation or "degreeBiased" for degree-biased sampling.

### Methods

#### `fit(net)`

Fit the negative edge sampler to the given network.

- `net` (numpy array or scipy sparse matrix): The input network for which the negative edge sampler will be fitted.

#### `sampling(size=None, source_nodes=None)`

Generate a dataset for link prediction by sampling positive and negative edges using the specified negative edge sampler.

- `size` (int, optional): Number of edges to sample. Defaults to None.
- `source_nodes` (list, optional): List of source nodes to condition negative edge sampling on. Required when `conditionedOnSource=True` in the initializer.

Returns:
- `pos_edges` (tuple): Tuple of node indices for positive edges.
- `neg_edges` (tuple): Tuple of node indices for negative edges.

## How the Negative Edge Sampler Works

The negative edge sampler generates negative edges by repeatedly sampling node pairs until the desired number of negative edges is obtained. The sampling is performed based on either uniform or degree-biased sampling, depending on the chosen method.

When `conditionedOnSource=True`, negative edge sampling is conditioned on a given list of source nodes. Otherwise, negative edges are sampled without conditioning.

The sampling process ensures that the generated negative edges do not contain self-loops, positive edges, or duplicates. Additionally, it keeps track of the already sampled negative edges to avoid duplicates during sampling.
