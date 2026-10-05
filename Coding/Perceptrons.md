
## The Basics

A **Perceptron** is the simplest type of artificial neural network used for **binary classification** (sorting data into one of two classes, such as `0` or `1`).

### How It Works

1. **Inputs ($x$):** Takes numerical features ($x_1, x_2, \dots$).
2. **Weights ($w$) & Bias ($b$):** Multiplies each input by an adjustable weight and adds a bias to calculate a weighted sum:
   $$z = (w_1 \ w_2 \ \dots)\cdot(x_1 \ x_2 \ \dots) = (w_1 x_1 + w_2 x_2 + \dots) + b$$
3. **Activation Function:** Passes the sum through a step function:
   * **Output = 1** if $\text{Sum} \ge 0$
   * **Output = 0** (or $-1$) if $\text{Sum} < 0$
### Intuition: Drawing a Line

Geometrically, a perceptron acts as a **linear boundary**:
* **2D:** A straight line separating two groups of dots.
* **3D+:** A flat plane (hyperplane) dividing classes in multi-dimensional space.
### How It Learns

* If it predicts **correctly**: No changes are made.
* If it predicts **incorrectly**: It adjusts weights and bias slightly with the **learning rate($\eta$)** toward the correct answer:
  $$w_1 =w_0 + \eta \, y \, x$$
$$b_1=b_0+\eta \,y$$

It then can repeat this as many times as necessary to refine its parameters
### Limitations

* **Linearly separable only:** A single perceptron can only solve problems where a single straight line can cleanly divide the classes (it cannot solve non-linear problems like **XOR** without adding hidden layers).

## An Example

### Given:
* **Input vector:** $x = [2, 1]$
* **Initial weights:** $w = [-1, 0]$
* **Initial bias:** $b = 0$
* **Target label:** $y = +1$
* **Learning rate:** $\eta = 1$
* **Activation rule:**
  $$\hat{y} = \begin{cases} +1 & \text{if } z \ge 0 \\ -1 & \text{if } z < 0 \end{cases}$$

### Forward Pass (Compute Prediction)
Calculate the weighted sum $z$:
$$z = w \cdot x + b = (-1 \times 2) + (0 \times 1) + 0 = -2$$
Apply the activation function:
$$\hat{y} = -1 \quad (\text{since } -2 < 0)$$

### Check Prediction
* **Target ($y$):** $+1$
* **Prediction ($\hat{y}$):** $-1$
* **Result:** Misclassified $\implies$ Update weights and bias.

### Update Weights and Bias
Using the update formulas: $$w_1 = w_0 + \eta \, y \, x$$ $$b_1 = b_0 + \eta \, y$$**Weight update ($w_1$):** $$w_1 = [-1, 0] + 1 \cdot (+1) \cdot [2, 1]$$ $$w_1 = [-1, 0] + [2, 1] = [1, 1]$$**Bias update ($b_1$):** $$b_1 = 0 + 1 \cdot (+1) = 1$$
### Result:
* **Updated weights:** $w = [1, 1]$
* **Updated bias:** $b = 1$