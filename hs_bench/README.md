# mapsforge hillshade algorithm compare

- mapsforgesrv: 0.22.0
- mapsforge-core: [master-c66a7d4](https://github.com/mapsforge/mapsforge/commit/c66a7d4faa2ed788a5f75ab4d25acaf2528907ae) _(next 0.22)_
- dataset: Digital Terrain Models 1" from [Sonny](https://drive.google.com/drive/folders/0BxphPoRgwhnoWkRoTFhMbTM3RDA?resourcekey=0-wRe5bWl96pwvQ9tAfI9cQg)
- JDK 23, Win11 x64
- AMD Ryzen 7 5700G *(integrated graphics Vega 512MB)*,  32GB@1800MHz
- `java.exe --patch-module java.desktop=libs\marlin-0.9.4.8-Unsafe-OpenJDK11.jar -Xmx8192m -Xms64m -Dsun.java2d.d3d=true -Dsun.java2d.opengl=false -Dsun.java2d.accthreshold=0 -Dsun.java2d.translaccel=true -Dsun.java2d.ddforcevram=true -Dsun.java2d.ddscale=true -Dsun.java2d.ddblit=true -Dsun.java2d.renderer.log=true -Dsun.java2d.renderer.useLogger=true -Dsun.java2d.renderer.profile=speed -Dsun.java2d.renderer.useThreadLocal=true -Dsun.java2d.renderer.useFastMath=true -Dsun.java2d.render.bufferSize=65536 -Dsun.java2d.renderer.pixelWidth=256 -Dsun.java2d.renderer.pixelHeight=256 -Dsun.java2d.renderer.tileSize_log2=8 -Dsun.java2d.renderer.tileWidth_log2=8 -Dawt.useSystemAAFontSettings=on`

### Notes
- Algorithms
  - **diffuselight**: [DiffuseLightShadingAlgorithm](https://github.com/mapsforge/mapsforge/blob/master/mapsforge-map/src/main/java/org/mapsforge/map/layer/hills/DiffuseLightShadingAlgorithm.java)
  - **simple**: [SimpleShadingAlgorithm](https://github.com/mapsforge/mapsforge/blob/master/mapsforge-map/src/main/java/org/mapsforge/map/layer/hills/SimpleShadingAlgorithm.java)
  - **simplasy**: [SimpleClasyHillShading](https://github.com/mapsforge/mapsforge/blob/master/mapsforge-map/src/main/java/org/mapsforge/map/layer/hills/SimpleClasyHillShading.java) - _Clear asymmetry_
  - **stdasy**: [StandardClasyHillShading](https://github.com/mapsforge/mapsforge/blob/master/mapsforge-map/src/main/java/org/mapsforge/map/layer/hills/StandardClasyHillShading.java) - _Clear asymmetry_
  - **hiresasy**: [HiResStandardClasyHillShading](https://github.com/mapsforge/mapsforge/blob/master/mapsforge-map/src/main/java/org/mapsforge/map/layer/hills/HiResStandardClasyHillShading.java) - _Clear asymmetry_
- for **Clear asymmetry** 
  -  `highQuality` param for **Clear asymmetry** algorithm
    - :warning: **hiresasy** + `highQuality = false` produce bad visual
    ![nono](.\hillshade_compare\hr_x-lq.png)
    - **stdasy** with `highQuality = false` or `highQuality = true` produce same visual results without time rendering difference. 
    - :fast_forward: Using `highQuality = true` for bench
  - `readingThreadsCount` & `computingThreadsCount` tunning doesn't impact bench time as the server is not under load
  - `asymmetryFactor` didn't affect visual results for **simpleasy**
- Bench time correspond to access log on server side without any other traffic on the 2nd _(hot)_ single hit 
- Algorithm prefixed with '*' correspond to default parameters
- Same bench results with **OpenGL** `sun.java2d.d3d=false -Dsun.java2d.opengl=true` & **Direct3D** `sun.java2d.d3d=true -Dsun.java2d.opengl=false`

## Results

![nono](.\hillshade_compare\nono.png)
`/14/8557/5822`

| algorithm    | rendering *(ms)* |
| ------------ | ---------------- |
| diffuselight | 152              |
| simple       | 152              |
| simplasy     | 152              |
| stdasy       | 152              |
| hiresasy     | 586              |


| algorithm        | parameter(s)                                               | tile                                            |
| ---------------- | ---------------------------------------------------------- | ----------------------------------------------- |
| * diffuselight | heightAngle: 50°                                             | ![nono](.\hillshade_compare\dl_50.png)          |
| diffuselight   | heightAngle: 0°                                              | ![nono](.\hillshade_compare\dl_0.png)           |
| diffuselight   | heightAngle: 30°                                             | ![nono](.\hillshade_compare\dl_30.png)          |
| diffuselight   | heightAngle: 60°                                             | ![nono](.\hillshade_compare\dl_60.png)          |
| diffuselight   | heightAngle: 90°                                             | ![nono](.\hillshade_compare\dl_90.png)          |
| <hr>           | <hr>                                                         | <hr>                                            |
| * simple       | linearity: 0.1, scale: 0.666                                 | ![nono](.\hillshade_compare\si_0.1-0.666.png)   |
| simple         | linearity: 0.0, scale: 0.5                                   | ![nono](.\hillshade_compare\si_0-0.5.png)       |
| simple         | linearity: 0.5, scale: 0.5                                   | ![nono](.\hillshade_compare\si_0.5-0.5.png)     |
| simple         | linearity: 1.0, scale: 0.5                                   | ![nono](.\hillshade_compare\si_1-0.5.png)       |
| simple         | linearity: 0.0, scale: 1.0                                   | ![nono](.\hillshade_compare\si_0-1.png)         |
| simple         | linearity: 0.5, scale: 1.0                                   | ![nono](.\hillshade_compare\si_0.5-1.png)       |
| simple         | linearity: 1.0, scale: 1.0                                   | ![nono](.\hillshade_compare\si_1-1.png)         |
| simple         | linearity: 0.0, scale: 2.0                                   | ![nono](.\hillshade_compare\si_0-2.png)         |
| simple         | linearity: 0.5, scale: 2.0                                   | ![nono](.\hillshade_compare\si_0.5-2.png)       |
| simple         | linearity: 1.0, scale: 2.0                                   | ![nono](.\hillshade_compare\si_1-2.png)         |
| <hr>           | <hr>                                                         | <hr>                                            |
| * hiresasy     | asymmetryFactor: 0.5, minSlope: 0, maxSlope: 80              | ![nono](.\hillshade_compare\hr_0.5-0-80-1.png)  |
| * stdasy       | asymmetryFactor: 0.5, minSlope: 0, maxSlope: 80              | ![nono](.\hillshade_compare\st_0.5-0-80-1.png)  |
| * simpleasy    | asymmetryFactor: 0.5, minSlope: 0, maxSlope: 80              | ![nono](.\hillshade_compare\sa_0.5-0-80-1.png)  |
| hiresasy       | asymmetryFactor: 0.5, minSlope: 0, maxSlope: 100             | ![nono](.\hillshade_compare\hr_0.5-0-100-1.png) |
| stdasy         | asymmetryFactor: 0.5, minSlope: 0, maxSlope: 100             | ![nono](.\hillshade_compare\st_0.5-0-100-1.png) |
| simpleasy      | asymmetryFactor: 0.5, minSlope: 0, maxSlope: 100             | ![nono](.\hillshade_compare\sa_0.5-0-100-1.png) |
| hiresasy       | asymmetryFactor: 0.5, minSlope: 20, maxSlope: 80             | ![nono](.\hillshade_compare\hr_0.5-20-80-1.png) |
| stdasy         | asymmetryFactor: 0.5, minSlope: 20, maxSlope: 80             | ![nono](.\hillshade_compare\st_0.5-20-80-1.png) |
| simpleasy      | asymmetryFactor: 0.5, minSlope: 20, maxSlope: 80             | ![nono](.\hillshade_compare\sa_0.5-20-80-1.png) |
| hiresasy       | asymmetryFactor: 0.5, minSlope: 20, maxSlope: 100            | ![nono](.\hillshade_compare\hr_0.5-20-100-1.png)|
| stdasy         | asymmetryFactor: 0.5, minSlope: 20, maxSlope: 100            | ![nono](.\hillshade_compare\st_0.5-20-100-1.png)|
| simpleasy      | asymmetryFactor: 0.5, minSlope: 20, maxSlope: 100            | ![nono](.\hillshade_compare\sa_0.5-20-100-1.png)|
| hiresasy       | asymmetryFactor: 0, minSlope: 0, maxSlope: 100               | ![nono](.\hillshade_compare\hr_0-0-100-1.png)   |
| stdasy         | asymmetryFactor: 0, minSlope: 0, maxSlope: 100               | ![nono](.\hillshade_compare\st_0-0-100-1.png)   |
| simpleasy      | asymmetryFactor: 0, minSlope: 0, maxSlope: 100               | ![nono](.\hillshade_compare\sa_0-0-100-1.png)   |
| hiresasy       | asymmetryFactor: 0.25, minSlope: 0, maxSlope: 100            | ![nono](.\hillshade_compare\hr_0.25-0-100-1.png)|
| stdasy         | asymmetryFactor: 0.25, minSlope: 0, maxSlope: 100            | ![nono](.\hillshade_compare\st_0.25-0-100-1.png)|
| simpleasy      | asymmetryFactor: 0.25, minSlope: 0, maxSlope: 100            | ![nono](.\hillshade_compare\sa_0.25-0-100-1.png)|
| hiresasy       | asymmetryFactor: 0.75, minSlope: 0, maxSlope: 100            | ![nono](.\hillshade_compare\hr_0.75-0-100-1.png)|
| stdasy         | asymmetryFactor: 0.75, minSlope: 0, maxSlope: 100            | ![nono](.\hillshade_compare\st_0.75-0-100-1.png)|
| simpleasy      | asymmetryFactor: 0.75, minSlope: 0, maxSlope: 100            | ![nono](.\hillshade_compare\sa_0.75-0-100-1.png)|
| hiresasy       | asymmetryFactor: 1, minSlope: 0, maxSlope: 100               | ![nono](.\hillshade_compare\hr_1-0-100-1.png)   |
| stdasy         | asymmetryFactor: 1, minSlope: 0, maxSlope: 100               | ![nono](.\hillshade_compare\st_1-0-100-1.png)   |
| simpleasy      | asymmetryFactor: 1, minSlope: 0, maxSlope: 100               | ![nono](.\hillshade_compare\sa_1-0-100-1.png)   |
