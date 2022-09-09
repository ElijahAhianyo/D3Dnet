# Code Data Utils

[_Documentation generated by Documatic_](https://www.documatic.com)

<!---Documatic-section-Codebase Structure-start--->
## Codebase Structure

<!---Documatic-block-system_architecture-start--->
```mermaid
None
```
<!---Documatic-block-system_architecture-end--->

# #
<!---Documatic-section-Codebase Structure-end--->

<!---Documatic-section-code.data_utils.add_light-start--->
## code.data_utils.add_light

<!---Documatic-section-add_light-start--->
<!---Documatic-block-code.data_utils.add_light-start--->
<details>
	<summary><code>code.data_utils.add_light</code> code snippet</summary>

```python
def add_light(img, light, *paras, mode):
    if mode == 'point':
        (x0, y0, radius) = paras
        light_res = np.zeros(3, radius, radius)
        for i in range(radius):
            for j in range(radius):
                light_res[0, i, j, 0] = light * (1 - math.sqrt((i - radius // 2) ** 2 + (j - radius // 2) ** 2) / radius)
                light_res[1, i, j, 0] = light * (1 - math.sqrt((i - radius // 2) ** 2 + (j - radius // 2) ** 2) / radius)
                light_res[2, i, j, 0] = light * (1 - math.sqrt((i - radius // 2) ** 2 + (j - radius // 2) ** 2) / radius)
        light_res = np.clip(light_res + img[:, x0 - radius // 2:x0 + 1 + radius // 2, y0 - radius // 2:y0 + 1 + radius // 2, :], 0, 255)
        img[:, x0 - radius // 2:x0 + 1 + radius // 2, y0 - radius // 2:y0 + 1 + radius // 2, :] = light_res
    return img
```
</details>
<!---Documatic-block-code.data_utils.add_light-end--->
<!---Documatic-section-add_light-end--->

# #
<!---Documatic-section-code.data_utils.add_light-end--->

<!---Documatic-section-code.data_utils.add_noise-start--->
## code.data_utils.add_noise

<!---Documatic-section-add_noise-start--->
<!---Documatic-block-code.data_utils.add_noise-start--->
<details>
	<summary><code>code.data_utils.add_noise</code> code snippet</summary>

```python
def add_noise(img, n_std):
    return img + np.random.normal(0, n_std, img.shape)
```
</details>
<!---Documatic-block-code.data_utils.add_noise-end--->
<!---Documatic-section-add_noise-end--->

# #
<!---Documatic-section-code.data_utils.add_noise-end--->

<!---Documatic-section-code.data_utils.random_crop-start--->
## code.data_utils.random_crop

<!---Documatic-section-random_crop-start--->
<!---Documatic-block-code.data_utils.random_crop-start--->
<details>
	<summary><code>code.data_utils.random_crop</code> code snippet</summary>

```python
def random_crop(HR, LR, patch_size_lr, scale_factor):
    (_, _, h_hr, w_hr) = HR.shape
    h_lr = h_hr // scale_factor
    w_lr = w_hr // scale_factor
    h_start_lr = random.randint(5, h_lr - patch_size_lr - 5)
    h_end_lr = h_start_lr + patch_size_lr
    w_start_lr = random.randint(5, w_lr - patch_size_lr - 5)
    w_end_lr = w_start_lr + patch_size_lr
    h_start = h_start_lr * scale_factor
    h_end = h_end_lr * scale_factor
    w_start = w_start_lr * scale_factor
    w_end = w_end_lr * scale_factor
    HR = HR[:, :, h_start:h_end, w_start:w_end]
    LR = LR[:, :, h_start_lr:h_end_lr, w_start_lr:w_end_lr]
    return (HR, LR)
```
</details>
<!---Documatic-block-code.data_utils.random_crop-end--->
<!---Documatic-section-random_crop-end--->

# #
<!---Documatic-section-code.data_utils.random_crop-end--->

<!---Documatic-section-code.data_utils.rgb2ycbcr-start--->
## code.data_utils.rgb2ycbcr

<!---Documatic-section-rgb2ycbcr-start--->
<!---Documatic-block-code.data_utils.rgb2ycbcr-start--->
<details>
	<summary><code>code.data_utils.rgb2ycbcr</code> code snippet</summary>

```python
def rgb2ycbcr(img, only_y=True):
    in_img_type = img.dtype
    img.astype(np.float32)
    if in_img_type != np.uint8:
        img *= 255.0
    if only_y:
        rlt = np.dot(img, [65.481, 128.553, 24.966]) / 255.0 + 16.0
    else:
        rlt = np.matmul(img, [[65.481, -37.797, 112.0], [128.553, -74.203, -93.786], [24.966, 112.0, -18.214]]) / 255.0 + [16, 128, 128]
    if in_img_type == np.uint8:
        rlt = rlt.round()
    else:
        rlt /= 255.0
    return rlt.astype(in_img_type)
```
</details>
<!---Documatic-block-code.data_utils.rgb2ycbcr-end--->
<!---Documatic-section-rgb2ycbcr-end--->

# #
<!---Documatic-section-code.data_utils.rgb2ycbcr-end--->

<!---Documatic-section-code.data_utils.ycbcr2rgb-start--->
## code.data_utils.ycbcr2rgb

<!---Documatic-section-ycbcr2rgb-start--->
<!---Documatic-block-code.data_utils.ycbcr2rgb-start--->
<details>
	<summary><code>code.data_utils.ycbcr2rgb</code> code snippet</summary>

```python
def ycbcr2rgb(ycbcr_img):
    ycbcr_img = ycbcr_img.numpy()
    in_img_type = ycbcr_img.dtype
    if in_img_type != np.uint8:
        ycbcr_img *= 255.0
    mat = np.array([[65.481, 128.553, 24.966], [-37.797, -74.203, 112.0], [112.0, -93.786, -18.214]])
    mat_inv = np.linalg.inv(mat)
    offset = np.array([16, 128, 128])
    rgb_img = np.zeros(ycbcr_img.shape)
    for x in range(ycbcr_img.shape[0]):
        for y in range(ycbcr_img.shape[1]):
            rgb_img[x, y, :] = np.maximum(0, np.minimum(255, np.round(np.dot(mat_inv, ycbcr_img[x, y, :] - offset) * 255.0)))
    return torch.from_numpy(np.ascontiguousarray(rgb_img.astype(np.float32) / 255))
```
</details>
<!---Documatic-block-code.data_utils.ycbcr2rgb-end--->
<!---Documatic-section-ycbcr2rgb-end--->

# #
<!---Documatic-section-code.data_utils.ycbcr2rgb-end--->

[_Documentation generated by Documatic_](https://www.documatic.com)