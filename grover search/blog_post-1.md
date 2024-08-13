# Quadratic Speedup Search Algorithm With [[4, 2, 2]] Quantum Error-Detecting code 

Grover's Search Algorithm [1](#References), introduced by Lov Grover in 1996, is one of the most important quantum algorithms due to its ability to search an unsorted database significantly faster than any classical algorithms. In classical computing, searching for a specific item among $N$ unsorted elements requires $O(N)$ steps. However, Grover's algorithm can achieve this in just $O(\sqrt{N})$ steps, providing a quadratic speedup. This remarkable efficiency is obtained by the unique properties of quantum computing: superposition and entanglement. These properties allow the algorithm to explore multiple possibilities simultaneously, therefore reducing the number of iterations needed to find the target item.

## The Challenge of Quantum Errors

Despite its power, Grover's search algorithm faces practical limitations due to errors inherent in current quantum hardware, such as quantum decoherence, gate imperfections, and environmental noise [2](#References). As quantum algorithms scale to handle larger datasets, these errors become increasingly problematic, potentially undermining the benefits of quantum speedup. To address these challenges, researchers have explored various techniques, including quantum error detection and correction, as well as quantum error mitigation, to enhance the reliability and accuracy of quantum computations on real hardware.

## Implementing Grover’s Search with Quantum Error Detection

Recently, Bibek Pokharel and Daniel A. Lidar [3](#References) have published a report on the successful implementation of Grover's search using quantum error detection (QED) codes and suppression. Inspired by their approach, in this blog post, I will implement the Grover search circuit for a 2-bit number on the Helmi quantum computer. This approach focuses on leveraging the specific layout of Helmi QPU chip to avoid the need for SWAP gates, which are often a source of errors in quantum superconducting computer. This means that the Helmi quantum computer can be used to search for a number in the unsorted set of $\{0, 1, 2, 3\}$ with just two steps.

In this implementation of Grover search, the [[4,2,2]] quantum error-detecting code is employed, utilizing a set of fault-tolerant gates to enhance the system's robustness [4](#References). These gates help mitigate the effects of quantum noise and operational imperfections, ensuring a more reliable and accurate execution of the algorithm. By incorporating these fault-tolerant techniques, we move closer to realizing the potential of theoretical quantum algorithms in practical quantum computing applications.

## Grover’s Search Algorithm Overview

Before diving into the implementation, let's briefly review Grover's Search Algorithm. The algorithm operates in three main stages:

1. **Initialization**: All qubits are initialized to a superposition state, representing all possible outcomes with equal probability.
2. **Oracle Application**: The oracle is a black-box function that identifies the correct answer by flipping the sign of the amplitude corresponding to the target state.
3. **Amplitude Amplification**: The algorithm repeatedly amplifies the probability of the correct answer by applying the Grover iteration, which consists of the oracle application followed by an inversion about the mean.

The number of iterations required to find the correct answer is proportional to $\sqrt{N}$ [1](#References), providing a significant speedup over classical search algorithms.

<div style="text-align: center;">
    <figure style="display: inline-block; text-align: left;">
        <img src="img/grover_2bit_example.png" alt="Example of Grover's 2-bit implementation">
        <figcaption>
            <p>
                <em> Figure 1: Grover's Search Algorithm for a 2-bit number with the oracle marking the state '01'. Since this is a search for a 2-bit number, only one iteration of the amplitude amplification step is required. </em>
            </p>
        </figcaption>
    </figure>
</div>

## Implementing Grover’s Search with the [[4, 2, 2]] Code

The [[4, 2, 2]] code is a quantum error-detecting code that encodes 2 logical qubits into 4 physical qubits [5](#References). This encoding allows us to detect errors, bit flips or phase flips, using specific syndromes. The syndromes in this quantum error detection code are $XXXX$ and $ZZZZ$ [6](#References).

In this implementation, the Grover search circuit is encoded using fault-tolerant gates. Fault-tolerant gates are specifically designed to prevent errors from propagating through the quantum circuit, making the entire computation more robust. See [this jupyternotebook](https://github.com/huyenemma/blogpost/blob/main/Grover_Search_error_detecting.ipynb) to know more about the implementation construction.

<div style="text-align: center;">
    <figure style="display: inline-block; text-align: left;">
        <img src="img/encode_v1.png" alt="Example of Grover's 2-bit implementation with [[4, 2, 2]] code">
        <figcaption>
            <p>
                <em>Figure 2: Implementation of Grover's Search Algorithm using the [[4, 2, 2]] quantum error-detecting code.</em>
            </p>
        </figcaption>
    </figure>
</div>

To validate the correctness of the encoded circuit, we ran the algorithm on an ideal simulator (no errors). The results confirmed a 100% accuracy for all oracle-marked states, demonstrating the effectiveness of the encoded fault-tolerant implementation.

<div style="text-align: center;">
    <figure style="display: inline-block; text-align: left;">
        <img src="img/simulator_enc.png" alt="Simulation results for Grover's Search with [[4, 2, 2]] code">
        <figcaption>
            <p>
                <em>Figure 3: The accuracy of Grover's Search Algorithm for different 2-bit numbers marked in the oracle state on a simulator.</em>
            </p>
        </figcaption>
    </figure>
</div>

## Running on the Helmi QPU

To compare the performance of the encoded and unencoded versions of the circuit, we ran both on the Helmi quantum computer. For a fair comparison, we duplicated the unencoded circuit (Figure 4) to match the qubit count of the encoded version.

<div style="text-align: center;">
    <figure style="display: inline-block; text-align: left;">
        <img src="img/unenc.png" alt="Unencoded Grover's 2-bit implementation">
        <figcaption>
            <p>
                <em>Figure 4: Unencoded Grover's search circuit for a 2-bit number</em>
            </p>
        </figcaption>
    </figure>
</div>

Although the [[4, 2, 2]] code provides syndromes $XXXX$ and $ZZZZ$ to detect errors, the Helmi QPU's limitation of 5 qubits prevents us from adding the necessary ancilla qubits for syndrome measurements. Despite this, the encoded circuit, utilizing fault-tolerant gate sets, still demonstrates improved performance compared to the unencoded version.

The results from the Helmi QPU indicate that the overall accuracy is below 50%, primarily due to the inherent noise and limitations of the hardware. Despite this, the encoded version outperforms the unencoded version (Fig. 6), demonstrating the advantages of incorporating error-detecting codes and fault-tolerant gates [7](#References), even in the absence of full error correction. However, it is important to note that results may vary, particularly in real hardware, as the calibration of the Helmi QPU can fluctuate over time.

<div style="text-align: center;">
    <figure style="display: inline-block; text-align: left;">
        <img src="img/helmi_result.png" alt="Results of Grover's search on Helmi">
        <figcaption>
            <p>
                <em> Figure 5: The accuracy of Grover's Search algorithm running on Helmi with the encoded version (left) and the unencoded version (right).</em>
            </p>
        </figcaption>
    </figure>
</div>

<div style="text-align: center;">
    <figure style="display: inline-block; text-align: left;">
        <img src="img/bar_helmi.png" alt="Results of Grover's search on Helmi">
        <figcaption>
            <p>
                <em> Figure 6: Compare the performance of encoded and unencoded version.</em>
            </p>
        </figcaption>
    </figure>
</div>

## Conclusion

The results demonstrate that encoding Grover’s Search Algorithm with the [[4, 2, 2]] quantum error-detecting code significantly improves its performance on noisy quantum hardware. Although the overall accuracy is limited by the hardware, the clear outperformance of encoded circuit suggests that error-detecting codes and fault-tolerant gates [8](#References) are crucial for designing a quantum circuit. As quantum computers continue to scale with more qubits, we can incorporate error syndrome measurements and even full error correction, further improving the algorithm’s reliability and accuracy. It is a good sign in realizing the full potential of quantum algorithms and making them practical for real-world applications.

## References

1. L. K. Grover, "Quantum mechanics helps in searching for a needle in a haystack," Phys. Rev. Lett., vol. 79, pp. 325-328, Jul. 1997. Available: https://journals.aps.org/prl/abstract/10.1103/PhysRevLett.79.325
2. A. Chatterjee, S. Das, and S. Ghosh, "Q-Pandora Unboxed: Characterizing Noise Resilience of Quantum Error Correction Codes,", Aug. 2023. Available: https://arxiv.org/pdf/2308.02769
3. B. Pokharel and D. A. Lidar, "Better-than-classical Grover search via quantum error detection and suppression," npj Quantum Inf., vol. 9, no. 1, Mar. 2023. Available: https://www.nature.com/articles/s41534-023-00794-6
4. E. G. Rieffel, D. A. Lidar, and A. Y. Matsuura, "Error detection on quantum computers improves accuracy of chemical calculations," arXiv:1910.00129, Oct. 2019. Available: https://arxiv.org/pdf/1910.00129
5. A. R. Calderbank and P. W. Shor, "Error prevention scheme with four particles," arXiv:quant-ph/9603031, Mar. 1996. Available: https://arxiv.org/pdf/quant-ph/9603031
6. D. Gottesman, "Protecting quantum memories using coherent parity check codes," arXiv:1709.01866, Sep. 2017. Available: https://arxiv.org/pdf/1709.01866#page=15.52
7. E. Hu, Z. Li, and R. Shapiro, "Quantum Benchmarking on the [[4,2,2]] Code," Duke University, Department of Mathematics, 2018. Available: https://services.math.duke.edu/DOmath/DOmath2018/hu-li-shapiro.pdf
8. A. D. Corcoles et al., "Fault-tolerant quantum error detection," Sci. Adv., vol. 3, no. 7, Jul. 2017. Available: https://www.science.org/doi/epdf/10.1126/sciadv.1701074
