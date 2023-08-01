# Link Prediction Dataset

This is a Python class that generates a link prediction dataset for evaluating link prediction models. It can be used to create train-test splits of a given network and sample negative edges for evaluation.

## Requirements

Make sure you have the following Python packages installed:

- `numpy`
- `pandas`
- `scipy`
- `tqdm`

## Usage

```python
from linkpred.node_samplers import ConfigModelNodeSampler, ErdosRenyiNodeSampler

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

Note: The code presented above assumes that you have the required packages installed and that you have implemented the necessary functions (e.g., `NegativeEdgeSampler`, `pairing`, `depairing`, etc.) outside of the provided code block.
