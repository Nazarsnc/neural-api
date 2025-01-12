# Only One Neuron - Source Code Examples

To make the learning of neural networks very easy, this folder contains 2 source code examples with neural networks that contain only one neuron:
* Compute the boolean OR operation.
* Compute the linear function 2*x + 3*y + 4.

## Computing the Boolean OR Operation
This example covers:
* data preparation (the training data),  
* neural network architecture,
* training and
* testing.

### The Training Data
The training data consists of all possible input combinations for the OR operation and their corresponding outputs:
```
- False OR False = False
- False OR True  = True
- True  OR False = True
- True  OR True  = True
```
The numeric values for True and False are defined with:
```
const
  cs_false = 0.1;                          // Encoding for "false" value
  cs_true  = 0.8;                          // Encoding for "true" value
  cs_threshold = (cs_false + cs_true) / 2; // Threshold for false/true (neuronal activation)
```

Then, the actual data structures with input and output of the OR operation are defined with:

```
type
  // Define the input and output types for training data
  TBackInput  = array[0..3] of array[0..1] of TNeuralFloat;  // Input data for OR operation
  TBackOutput = array[0..3] of array[0..0] of TNeuralFloat;  // Expected output for OR operation

const
  cs_inputs : TBackInput =
  (
    // Input data for OR operation
    (cs_false, cs_false),
    (cs_false, cs_true),
    (cs_true,  cs_false),
    (cs_true,  cs_true)
  );

const
  cs_outputs : TBackOutput =
  (
    // Expected outputs for OR operation
    (cs_false),
    (cs_true),
    (cs_true),
    (cs_true)
  );
```

### Neural Network Architecture

The neural network consists of only 2 layers:
* an input layer with two inputs (representing the two inputs of the OR operation)
* and a single output neuron that provides the result. It uses a fully connected architecture without activation function.

The above neural network is created with:
```
  NN := TNNet.Create();
  // Create the neural network layers
  NN.AddLayer(TNNetInput.Create(2));                     // Input layer with 2 inputs
  NN.AddLayer(TNNetFullConnectLinear.Create(1));         // Single neuron layer connected to both inputs from the previous layer.
```

The activation function is not required when the problem to be learned can be solved via linear algebra.

### Training the Neural Network
The training is done with:
```
  vInputs := cs_inputs;                                  // Assign the input data
  vOutput := cs_outputs;                                 // Assign the expected output data
  pOutPut := TNNetVolume.Create(1, 1, 1, 1);             // Create a volume to hold the computed output
  NN.SetLearningRate(0.01, 0.9);                         // Set the learning rate and momentum

  for EpochCnt := 1 to 600 do
  begin
    for Cnt := Low(cs_inputs) to High(cs_inputs) do
    begin
      // Feed forward and backpropagation
      NN.Compute(vInputs[Cnt]);                          // Perform feedforward computation
      NN.GetOutput(pOutPut);                             // Get the output of the network
      NN.Backpropagate(vOutput[Cnt]);                    // Perform backpropagation to adjust weights

      if EpochCnt mod 100 = 0 then
        WriteLn(
          EpochCnt:7, 'x', Cnt,
          ' Inputs:', vInputs[Cnt][0]:5:3,' ',vInputs[Cnt][1]:5:3,
          ' Computed Output:', pOutPut.Raw[0]:5:2,' ',
          ' Desired Output:', vOutput[cnt][0]:5:2
        );
    end;

    if EpochCnt mod 100 = 0 then
      WriteLn();
  end;
```
After running the above code, the ouput is:
```
The value encoding FALSE is: 0.10
The value encoding TRUE is: 0.80
The threshold is: 0.45

    600x0 Inputs:0.100 0.100 Computed Output: 0.27  Desired Output: 0.10
    600x1 Inputs:0.100 0.800 Computed Output: 0.62  Desired Output: 0.80
    600x2 Inputs:0.800 0.100 Computed Output: 0.63  Desired Output: 0.80
    600x3 Inputs:0.800 0.800 Computed Output: 0.98  Desired Output: 0.80
```
All values above 0.45 are considered the boolean value `True` while all values below 0.45 are considered `False`. We got the result 100% right.
