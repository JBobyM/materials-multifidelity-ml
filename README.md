# Multi-Fidelity Machine Learning for Materials Discovery

Reducing materials characterization costs by 60% using machine learning to predict expensive high-fidelity measurements from cheap low-fidelity data.

**✨ Validated on 2,500 real materials from Materials Project database**

---

## 🎯 Problem Statement

Materials characterization is a critical bottleneck in materials discovery:
- **High-fidelity measurements** (r2SCAN DFT, slow XRD, high-res spectroscopy) are accurate but expensive and slow
- **Low-fidelity measurements** (PBE DFT, fast XRD, low-res spectroscopy) are cheap and fast but less accurate
- **Research question**: Can we use ML to get high-fidelity accuracy at low-fidelity cost?

This project addresses three key questions from materials discovery research:

1. **Can we use faster, rougher characterization techniques to approximate expensive measurements?**
2. **Can we use multiple fast readings to predict a higher resolution reading?**
3. **Can synthesis information and computational simulations be incorporated to predict high-res readings?**

---

## 📊 Key Results

### Synthetic Data (Proof of Concept)

| Approach | MAE (eV/atom) | RMSE (eV/atom) | R² | Improvement |
|----------|---------------|----------------|-----|-------------|
| **Baseline** (High-Fidelity Only) | 1.022 | 1.299 | 0.898 | - |
| **Delta Learning** | 0.241 | 0.305 | 0.993 | **+76.4%** |
| **Transfer Learning** | 0.208 | 0.260 | 0.995 | **+79.7%** |

### Real Materials Project Data (2,500 Materials)

| Approach | MAE (eV/atom) | RMSE (eV/atom) | R² | Improvement |
|----------|---------------|----------------|-----|-------------|
| **Baseline** (High-Fidelity Only) | 0.466 | 0.643 | 0.507 | - |
| **Delta Learning** ⭐ | 0.133 | 0.163 | 0.974 | **+71.4%** |
| Transfer Learning | 0.557 | 0.755 | 0.453 | -19.6% |

**Real materials tested**: Ac, AlNi₂, Al₂O₃, Ac₂AgIr, Ac₂CdGa, GaAs, and 2,494+ more

**Key Finding**: Delta learning significantly outperforms baseline on real data. Transfer learning underperformed due to limited dataset size (needs 5k+ samples to be effective).

### Cost-Benefit Analysis

**Scenario**: Screening 1,000 new materials

| Approach | Computational Cost | Time @ 100 cores | Savings |
|----------|-------------------|------------------|---------|
| **Traditional** (all high-fidelity) | 50,000 CPU hours | 20.8 days | - |
| **Multi-fidelity** (selective verification) | 20,000 CPU hours | 8.3 days | **60%** |

**Result**: 12.5 days faster, 30,000 CPU hours saved, AND 71.4% better accuracy with delta learning!

---

## 🔬 Technical Approach

### Three ML Strategies Implemented

1. **Baseline Model**
   - Trains only on limited high-fidelity data
   - Represents traditional approach
   - Establishes performance benchmark

2. **Delta Learning (Bias Correction)** ⭐ Best Performer
   - Predicts the correction: Δ = y_high - y_low
   - Learns systematic bias between fidelity levels
   - **76% improvement on synthetic data**
   - **71% improvement on real materials**
   - Winner on both synthetic and real data!

3. **Transfer Learning**
   - Pre-trains on abundant low-fidelity data (10,000 samples)
   - Fine-tunes on limited high-fidelity data (600 samples)
   - 80% improvement on synthetic data
   - -20% on real data (underperformed - needs larger datasets)
   - Lesson: Works great with enough data, but delta learning is more robust

4. **Uncertainty Quantification**
   - Ensemble-based uncertainty estimates
   - Identifies which materials need expensive verification
   - Enables active learning workflows

---

## 🚀 Quick Start

### Installation

```bash
# Clone repository
git clone <your-repo-url>
cd "UL Material Discovery"

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### Run Analysis

**Option 1: Interactive Jupyter Notebooks**
```bash
jupyter notebook
```
Then open:
- `multi_fidelity_materials_prediction.ipynb` - Full demo with explanations
- `run_notebook.ipynb` - Synthetic data analysis (~30 seconds)
- `run_notebook_real_data.ipynb` - Real materials validation (~1 minute)

**Option 2: Execute Notebooks Programmatically**
```bash
# Synthetic data
jupyter nbconvert --to notebook --execute run_notebook.ipynb --inplace

# Real materials data
jupyter nbconvert --to notebook --execute run_notebook_real_data.ipynb --inplace
```

---

## 📁 Project Structure

```
.
├── 📓 Notebooks
│   ├── multi_fidelity_materials_prediction.ipynb  # Complete interactive demo
│   ├── run_notebook.ipynb                         # Synthetic data analysis
│   ├── run_notebook_real_data.ipynb               # Real materials validation
│   └── fetch_real_data.ipynb                      # Materials Project data fetcher
│
├── 📊 Data
│   └── materials_project_real_data.csv            # 2,500 real materials
│
├── 📖 Documentation
│   ├── README.md                                  # This file
│   ├── HOW_TO_USE.md                              # Detailed usage guide
│   ├── RESULTS_SUMMARY.md                         # Synthetic data results
│   ├── REAL_DATA_RESULTS.md                       # Real data analysis
│   └── SETUP_REAL_DATA.md                         # Materials Project setup
│
└── ⚙️ Configuration
    ├── requirements.txt                           # Python dependencies
    └── .gitignore                                 # Git ignore rules
```

---

## 💡 How It Answers the Research Questions

### Question 1: Faster techniques to approximate expensive measurements?

**✅ YES** - Delta learning achieved 71.4% improvement on real materials
- Low-fidelity PBE (5 CPU hrs) predicts high-fidelity r2SCAN (50 CPU hrs)
- R² improved from 0.507 (baseline) to 0.974 (delta learning)
- Best predictions within 0.001 eV/atom (publication quality)
- **Practical application**: Fast XRD scan (5 min) → Predict slow scan (2 hours)

### Question 2: Multiple fast readings to predict higher resolution?

**✅ YES** - Delta learning uses both low-fidelity predictions AND material features
- Combines cheap measurements with composition/structure data
- 76% improvement on synthetic data, 71% on real materials
- Uncertainty quantification (Random Forest ensemble) identifies which materials need verification
- High-confidence predictions (50% of samples) have MAE of 0.114 eV/atom
- **Practical application**: Multiple sensor readings → Predict final properties

### Question 3: Incorporate simulations to predict high-res readings?

**✅ YES** - Multi-fidelity framework combines multiple data sources
- Material features (composition, structure, band gap, density)
- Low-fidelity predictions (PBE calculations)
- Computational simulations (DFT at different theory levels)
- Learned systematic correction: -0.305 eV/atom bias
- **Practical application**: Synthesis conditions + in-situ sensors + quick tests → Predict final quality

---

## 🎓 Technical Highlights

### Machine Learning Techniques
- Gradient Boosting (scikit-learn)
- Multi-layer Perceptrons for transfer learning
- Random Forests for uncertainty quantification
- Ensemble methods for prediction confidence

### Materials Science Domain
- DFT calculations (PBE vs r2SCAN functionals)
- Formation energy predictions (eV/atom)
- Crystal structure descriptors (cubic, hexagonal, etc.)
- Materials Project API integration

### Software Engineering
- Reproducible Jupyter notebooks
- Modular Python code
- Comprehensive documentation
- Version controlled with Git

---

## 📈 Extensibility to Lab Applications

This framework is ready for real lab characterization data:

### Spectroscopy
- **Low-res → High-res**: 100 wavelength points → 1,000 points
- **Fourier features**: Extract frequency-domain features from spectra
- **Multiple modalities**: Combine Raman + IR + UV-Vis

### X-Ray Diffraction (XRD)
- **Fast scan → Slow scan**: 5° to 40° (5 min) → 5° to 120° (2 hours)
- **Pattern prediction**: Predict missing peaks
- **Crystal structure**: Determine phase from partial data

### Microscopy
- **Low-mag → High-mag**: Quick overview → Detailed features
- **Image enhancement**: Denoise and super-resolution
- **Automated screening**: Focus expensive imaging on interesting samples

### Sensor Fusion
- **Time-series data**: In-situ sensors during synthesis
- **Multi-sensor**: Temperature, pressure, optical emission
- **Process optimization**: Predict outcome before completion

---

## 🔧 Requirements

```
Python 3.8+
numpy >= 1.24.0
pandas >= 2.0.0
scikit-learn >= 1.3.0
matplotlib >= 3.7.0
seaborn >= 0.12.0
jupyter >= 1.0.0
mp-api >= 0.41.0  # For Materials Project integration
```

See `requirements.txt` for complete list.

---

## 📚 Materials Project Integration

This project uses real data from the Materials Project database:
- 2,500 materials with DFT calculations
- Formation energies, band gaps, crystal structures
- Free API access at https://next-gen.materialsproject.org/api

To fetch fresh data:
1. Get free API key from Materials Project
2. Add to `.env` file: `MP_API_KEY="your_key_here"`
3. Run `fetch_real_data.ipynb`

---

## 🎯 Use Cases

### Research & Development
- Accelerate materials screening for batteries, catalysts, solar cells
- Reduce computational costs for high-throughput DFT
- Enable larger-scale materials exploration

### Quality Control
- Fast prediction of material properties from quick tests
- Identify anomalies that need detailed characterization
- Real-time process monitoring and optimization

### Active Learning
- Uncertainty-driven experiment selection
- Iterative improvement with new data
- Closed-loop autonomous laboratories

---

## 👨‍🎓 Author

**Education**: MSc Statistics - Machine Learning, University of Ottawa (2025)

**Skills Demonstrated**:
- Multi-fidelity machine learning
- Transfer learning and model fine-tuning
- Uncertainty quantification
- Materials science domain knowledge
- Scientific computing and data analysis
- Professional documentation and communication

---

## 📄 License

This project is available for educational and research purposes.

---

## 🙏 Acknowledgments

- **Materials Project** for providing open materials data
- **University of Ottawa** for training and education
- **UL Research Team** for inspiring this project through their presentation

---

## 📧 Contact

For questions about this project or collaboration opportunities, please reach out via GitHub issues.

---

## 🔗 Citations

If you use this work, please cite:
- Materials Project: Jain, A. et al. (2013). APL Materials, 1, 011002.
- Multi-fidelity ML: Chen, C. et al. (2021). Nature Computational Science, 2, 46-53.

---

**Last Updated**: October 2025
