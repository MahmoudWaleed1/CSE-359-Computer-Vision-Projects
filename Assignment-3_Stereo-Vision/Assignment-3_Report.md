# CSE 359 — Assignment 3 (Stereo Vision)

## 1. Data
The stereo pairs are located in the materials folders:

- Pair 1: [Part-1/stereo_materials/l1.png](Part-1/stereo_materials/l1.png) and [Part-1/stereo_materials/r1.png](Part-1/stereo_materials/r1.png)
- Pair 2: [Part-1/stereo_materials/l2.png](Part-1/stereo_materials/l2.png) and [Part-1/stereo_materials/r2.png](Part-1/stereo_materials/r2.png)
- Pair 3: [Part-1/stereo_materials/l3.png](Part-1/stereo_materials/l3.png) and [Part-1/stereo_materials/r3.png](Part-1/stereo_materials/r3.png)

Assumption: the images are **rectified**, so corresponding points lie on the same row, and disparity is mostly horizontal.

## 2. Part 1 — Block Matching (SAD / SSD)
### 2.1 Idea (in my words)
For each pixel $(x,y)$ in the left image, I take a small window around it and slide the same-size window in the right image (only horizontally). For every shift $d$, I compute a matching cost, then pick the $d$ with the smallest cost. That $d$ becomes the disparity for that pixel.

### 2.2 Cost functions used
Let the window size be $w\times w$ and $k=\lfloor w/2\rfloor$.

- **SAD**
  $$C_{\text{SAD}}(x,y,d)=\sum_{u=-k}^{k}\sum_{v=-k}^{k}\left|I_L(x+u,y+v)-I_R(x-d+u,y+v)\right|$$

- **SSD**
  $$C_{\text{SSD}}(x,y,d)=\sum_{u=-k}^{k}\sum_{v=-k}^{k}\left(I_L(x+u,y+v)-I_R(x-d+u,y+v)\right)^2$$

Then I choose:
$$d^*(x,y)=\arg\min_{d\in[0,D_{max})} C(x,y,d)$$

### 2.3 Implementation details
All code is in [Part-1/Block-Matching.ipynb](Part-1/Block-Matching.ipynb).

- Convert images to grayscale.
- Brute-force nested loops over pixels and disparities.
- I used `max_disp = 64` (so $D_{max}=64$).
- I tested different odd window sizes, including a sweep: $w \in \{1,3,5,\dots,15\}$.

### 2.4 What I noticed from the results
Because the notebook shows the disparity maps as images, I mainly compared quality visually:

- **Effect of window size**
  - Small windows (like $w=1$ or $w=3$) give noisier maps, especially on flat/low-texture areas.
  - Larger windows reduce noise but start to blur depth boundaries (object edges look thicker).

- **SAD vs SSD**
  - SSD usually reacts more strongly to big intensity differences, so sometimes it looks harsher around outliers.
  - SAD can look slightly more stable in some areas, but both still fail in textureless regions.

- **Common problems**
  - Textureless regions: many pixels look similar, so matching becomes ambiguous.
  - Repetitive patterns: the best match may be wrong but still has a low cost.
  - Occlusions: block matching still forces a match even when a pixel has no real correspondence.

### 2.5 Runtime note
This implementation is brute force, so it becomes slow when increasing window size or the disparity range. The rough complexity is:
$$\mathcal{O}(H\cdot W\cdot D\cdot w^2)$$

## 3. Part 2 — Dynamic Programming Scanline Stereo
### 3.1 Idea (in my words)
Instead of picking the best match independently for each pixel, DP tries to find the best overall matching along each **row** (scanline). It fills a cost table for matching left row pixels to right row pixels, and it also allows “skipping” pixels to represent occlusions.

### 3.2 DP formulation used
The notebook computes a match cost (squared difference) and uses a constant penalty for skipping.

- Match cost in the code:
  $$d(i,j)=\frac{(L[i]-R[j])^2}{4}$$
- Occlusion/skip penalty in the code: $\lambda=1$

DP recurrence (as implemented):
$$\text{costs}[i,j]=\min\big(\text{costs}[i-1,j-1]+d(i,j),\;\text{costs}[i-1,j]+\lambda,\;\text{costs}[i,j-1]+\lambda\big)$$

After filling the table, the code backtracks. When it decides a pixel is matched, the disparity assigned is:
$$\text{disp}[i]=|i-j|$$

### 3.3 Implementation details
All code is in [Part-2/stereo_db.ipynb](Part-2/stereo_db.ipynb).

- Convert both images to grayscale.
- For each row, build the DP table and backtrack to get a 1D disparity array.
- Stack all rows to form the final disparity map.

### 3.4 What I noticed from the results
- The DP approach can handle occlusions better than plain block matching because it can “skip” pixels.
- Since the method is run independently per row, it can still create **horizontal streaks** (row-by-row artifacts).
- The skip penalty value ($\lambda=1$) affects behavior; different values could change how often the algorithm prefers skipping vs matching.

## 4. Quick comparison (Part 1 vs Part 2)
- **Block matching** is simpler and easy to understand, but it struggles more with occlusions and depends a lot on window size.
- **DP scanline** is more “global” per row and can model occlusion, but it can be expensive and still produces scanline artifacts because there is no 2D smoothness term.

## 5. How to run
- Part 1: [Part-1/Block-Matching.ipynb](Part-1/Block-Matching.ipynb)
- Part 2: [Part-2/stereo_db.ipynb](Part-2/stereo_db.ipynb)

Run all cells in each notebook. The notebooks use `opencv-python`, `numpy`, and `matplotlib`.

## 6. Conclusion
Overall, both methods produce reasonable disparity maps on textured areas, but both struggle in classic hard cases like low-texture regions and depth boundaries. Block matching is a good baseline, while DP scanline is a step toward handling occlusion more properly.
