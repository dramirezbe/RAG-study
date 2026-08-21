# Minimization of measuring points for the electric field exposure map generation in indoor environments by means of Kriging interpolation and selective sampling

**Journal:** *Environmental Research*, Volume 212, Part 1, 2022, 113577  
**DOI:** [10.1016/j.envres.2022.113577](https://doi.org/10.1016/j.envres.2022.113577)  
**Authors:** A. Martínez-González\*, J. Monzó-Cabrera, A.J. Martínez-Sáez, A.J. Lozano-Guerrero  
**Affiliation:** Electromagnetics and Matter Group, Universidad Politécnica de Cartagena, Campus Muralla, Cartagena, E-30202, Spain  
\* **Corresponding Author:** toni.martinez@upct.es (A. Martínez-González)  
**Article History:** Received 15 March 2022; Revised 5 May 2022; Accepted 25 May 2022; Available online 28 May 2022  

---

## Abstract

In a world with increasing systems accessing to radio spectrum, the concern for exposure to electromagnetic fields is growing and therefore it is necessary to check limits in those areas where electromagnetic sources are working. Therefore, radio and exposure maps are continuously being generated, mainly in outdoor areas, by using many interpolation techniques. In this work, Surfer software and Kriging interpolation have been used for the first time to generate an indoor exposure map. A regular measuring mesh has been generated. Elimination of Less Significant Points (ELSP) and Geometrical Elimination of Neighbors (GEN) strategies to reduce the measuring points have been presented and evaluated. Both strategies have been compared to the map generated with all the measurements by calculating the root mean square and mean absolute errors. Results indicate that ELSP method can reduce up to 70% of the mesh measuring points while producing similar exposure maps to the one generated with all the measuring points. GEN, however, produces distorted maps and much higher error indicators even for 50% of eliminated measuring points. As a conclusion, a procedure for reducing the measuring points to generate radio and exposure maps is proposed based on the ELSP method and the Kriging interpolation.

**Keywords:** Electromagnetic field radiation map, Kriging interpolation, Spatial data analysis, Non-ionizing radiation, Measuring point reduction.

---

## 1. Introduction

The electromagnetic (EM) environment in cities has experienced an explosive growth since the 1990s due to mobile communications and the usage of wireless technologies for internet access (Santana et al., 2017). Thus, the signal of these cellular systems is overlapping with other radio signals from TV, FM, AM, Bluetooth, satellite communications, radar, navigation systems, etcetera.

Additionally, nowadays, it is very common to find radiofrequency and microwave communication systems in workplaces, hospitals, educational institutions, both outdoors and indoors and it seems that Internet of Things (IoT) will boost this trend with 5G networks, services, and devices. In a near future, this might bring problems of compatibility, with self and mutual interference among services and devices.

Because of this exponential development, a growing concern about safety of EM fields is being observed in the population, institutions, firms, local, regional, and national governments (Röösli et al., 2010). In parallel, the scientific and technical community has established safe EM field exposure levels in different institutions, for both the workplace and the general public, such as the International Commission on Non-Ionizing Radiation Protection (ICNIRP, 2020), the Institute of Electrical and Electronics Engineers (IEEE, 2019), the Federal Communications Commission (Cleveland and Ulcek, 1999), or the European Union (EU) (Recommendation 1999/519/EC; Directive 2013/35/EU).

The exposure levels can be measured with specific instruments such as electric or magnetic field probes that consider all the electromagnetic sources in a frequency range (ICNIRP, 1998; Pinheiro et al., 2015; ITU, 2014) although other instruments such as spectrum analyzers may be used when specific frequency ranges or services are to be studied.

When these measurements are extended and geopositioned within a wide area, radioelectric maps can be created indicating the distribution of exposure levels to EM fields during the measuring period. Therefore, radioelectric maps are a spatial characterization of the EM field strength or power density distribution in open or closed locations within a frequency range. There is a wide application of these radio maps such as interference avoidance and location, spectrum usage monitoring, human exposure to EM fields (Shan et al., 2018) or, even, in epidemiological studies (Gonzalez-Rubio et al., 2016).

Unfortunately, when important areas are to be mapped the number of measurements might be too high to be cost and time effective and therefore the usage of propagation models and/or spatial interpolation methods are useful to estimate the electric field in those locations where measurements have not been carried out. For instance, to carry out a radio map that evaluates the exposure levels in an environment, it is necessary to estimate the EMFs intensity levels within the location as a function of the spatial distribution of the measurements (de Andrade et al., 2020).

Many works have carried out exposure measurements and radio maps in outdoor locations. The mapping of electric and magnetic field levels with three spatial interpolation methods: Kriging (KG), natural neighbor interpolation (NNI) and inverse distance weight (IDW), was evaluated on an air-insulated substation supply circuit that operates at 230 kV / 60 Hz in de Andrade et al. (2020). This work indicates that whereas NNI is the best interpolation method for the magnetic field, the Gaussian KG method interpolates the electric field better than any other method. In Safigianni and Tsompanidou (2009) electric and magnetic field levels have been measured at extremely low frequencies (ELF) inside an outdoor electric power substation.

Inverse distance to a power (IDP), KG, Minimum Curvature (MC), Modified Shepard’s Method (MSM) and Radial Basis Function (RBF) interpolation methods were analyzed by using the cross-validation method with the electric field measurements in the central region of the city of Mossoró with a frequency range from 10 MHz to 8 GHz (Santana et al., 2017). This work shows that both IDP and KG methods were the ones providing the best results for interpolation. KG has been also applied in Paniagua et al. (2013) in an outdoor scenario where the results indicate that, in the urban area analyzed, the linear density of sampling points could be reduced.

Inverse Distance Weighted (IDW), spline and ordinary KG methods have been employed in Rufo et al. (2018) to map the electric field levels for 3 AM emission frequencies (774 kHz, 900 kHz, and 1107 kHz) from spectrum analyzer measurements. Authors show that the optimization of the characteristic parameters of the interpolation methods allows employing any of them, although, in this study, IDW showed the best predictions.

In Gonzalez-Rubio et al. (2016) a personal exposimeter, Satimo EME Spy 140 model, and ArcGIS software were employed to record and map the average exposure levels of GSM, DCS and UMTS systems in the city of Albacete (Spain). Other works, such as Garcia-Moreta et al. (2019), use machine learning regression algorithms to map and predict digital terrestrial television coverage in Quito (Ecuador).

The specific usage of the KG interpolation method to build exposure and radio maps is described in several works. Shan et al. (2018) design a method of electromagnetic environment map construction based on KG spatial interpolation and the obtained results are compared to Modified Shepard and IDW methods. In that work, KG obtains the minimum root-mean-square error between interpolated and measured values. Ould-Isselmou et al. (2006) use KG interpolation with external drift, usually named as KED, in order to build an EM exposure map in the streets of Paris for 9 bands including GSM 900, GSM 1800 and UMTS downlink. Sato et al. (2019) use the KG interpolation technique to reconstruct a radio environment map (REM). The authors conclude that the error in the received power estimation over log-normal channels can be modeled using a correlation coefficient when the interpolation coefficients are optimized. Additionally, in Sato and Fujii (2017), ordinary Kriging interpolation is employed to build a REM that allows studying the spatial spectrum sharing. The allowable interference power to the primary user is approximately calculated from the predicted distribution of the estimated error. In Sato et al. (2021) authors extend the Kriging-based radio map spatial interpolation to space-frequency interpolation and extrapolation, estimating, in this way, accurate radio maps on frequency bands where no (or a few) datasets are available with data obtained from other frequency bands. Finally, a comparison of spline, IDW and KG interpolation methods of the average electric field intensity in an outdoor area of Caracas (Venezuela) is carried out in Azpurua and Dos Ramos (2010) concluding that IDW performs better than KG and spline in the bandwidth of 100 kHz–6 GHz.

All the previous works have been performed in outdoor scenarios but, to the best knowledge of authors, no evaluation of KG interpolation techniques has been performed to build exposure radio maps within indoor facilities. In fact, there are few works that investigate interpolation techniques to create indoor radio or exposure maps. Solin et al. (2018) derive and use a Bayesian nonparametric probabilistic modeling approach for interpolation and extrapolation of the magnetic field in the lobby of a building at the Aalto University campus in Espoo (Finland).

In this work, the point ordinary KG interpolation technique has been applied to create an indoor radio exposure map for the average electric field magnitude inside the library of the Telecommunication Engineering School at Universidad Politécnica de Cartagena, Cartagena (Spain). The interpolation was based on measurements carried out with a Narda NBM-550 broadband field meter and isotropic Narda EF0691 E-field probe in the frequency range 100 kHz–6 GHz. This work also assesses two methods for reducing the needed measurement locations based on the electric field intensity levels. The obtained results indicate that measurements in indoor locations should be prioritized at those areas with higher electric field magnitude values. With this strategy, it is possible to reduce the number of locations to be measured up to a 70% obtaining, in terms of both root mean square error (RMSE) and mean absolute error (MAE), very similar exposure maps to that obtained with a regular measurement mesh.

---

## 2. Materials and Methods

### 2.1. Indoor Location and Measurement Points

Indoor measurements were carried out at the library reading room of the Telecommunication Engineering School (ETSIT) at Universidad Politécnica de Cartagena, in Cartagena (Spain). Latitude and longitude coordinates of ETSIT are $37.598238^\circ$ and $-0.987412^\circ$, respectively. Fig. 1 shows the floor plan of the building and the layout of the rooms. The red discontinuous line indicates the area where the measurements were acquired.

Up to 386 different locations were used to measure the average electric field intensity inside the reading room trying to follow a 2D rectangular mesh. Some locations could not be used due to fixed furniture, mainly tables. Fig. 2 shows the points where measurements were located.

The origin of coordinates was in the lower left corner of the reading room plan. Purple symbols indicate where routers were installed. The total area of this room is $923.87\text{ m}^2$ whereas the area where measurements could be performed (useable area) was $691.92\text{ m}^2$. Both $x$ and $y$ separation between measurement points was $0.3\text{ m}$. The measurements were performed without any student or university personnel inside the library reading room to facilitate the procedure.

Therefore, the measured electric field magnitude was due, exclusively, to the radiating devices inside or near the location. Narrowband measurements have been carried out to confirm the presence of different sources in the environment by using a FSH4 Spectrum Analyzer from Rohde & Schwarz. It must be remarked that the main radiation sources in the library reading room were Wi-Fi routers accomplishing the 802.11g standard protocol that provide internet access to students and university personnel at the 2.45 GHz band. Wi-Fi routers carrier levels were more than 8 dB above FM band, 12 dB above GSM band or 16 dB above LTE band signals which were the most significative ones. It should be noted, however, that the proposed procedure should be independent of the knowledge of the radiation sources.

In each location, the electric field magnitude was averaged for 6 min (1 sample per second) and, therefore, the results shown in this work are a time and frequency average of the electric field magnitude.

> **Fig. 1.** Floor plan and layout of the rooms of ETSIT at Universidad Politécnica de Cartagena, Cartagena (Spain). Discontinuous red line indicates the library reading room where measurements were carried out.

> **Fig. 2.** Measuring points within the reading room of the library. Purple symbols: routers. Blue squares: areas used for mean values in Table 1.

---

### 2.2. Measurement Instrumentation and Procedure

Authors used a Narda NBM-550 broadband field meter and isotropic Narda EF0691 E-field probe to measure the average RMS E-field within the 100 kHz–6 GHz bandwidth. The main specifications of this measuring set are:

- **Electric Field Range:** 0.2 V/m to 650 V/m
- **Linearity:** $\pm 0.5\text{ dB}$
- **Isotropic response:** $\pm 1\text{ dB}$

Fig. 3 shows the Narda field meter and field probe at a measurement point inside the library reading room and the height of the measurements, which in this case was set to $170\text{ cm}$.

The measurements followed the procedure indicated in the EN 50413:2019 measurement standard. As indicated in this standard, all relevant frequency components and field sources were included in the bandwidth of the used instrumentation and therefore the measurement levels included the total field strength of all spectral components within the measurement frequency range. A preliminary survey of the indoor location was carried out to identify the areas with maximum levels of electric field intensity and then the measuring location matrix described in the previous section was defined to carry out the measurements.

It should be remarked that an important number of measurement locations showed electric field levels below the sensitivity of the device ($0.2\text{ V/m}$). Several options are available when dealing with those non-detects, such as excluding them from the study, using the electric field value provided by the field meter, setting those measurements to zero or $0.2\text{ V/m}$, assuming a statistical distribution and using fitting methods with the detected data to provide estimations for these points as shown in Röösli et al. (2008), or other strategies as the ones reported in Nájera et al. (2020). Each of these options will have an important impact on the obtained mean values. In this work, authors have used the electric field level provided by the Narda equipment even when this level is below the $0.2\text{ V/m}$ sensitivity since this helps in the implementation of the analyzed minimization procedures, which are explained in section 2.5.

> **Fig. 3.** Used Narda NBM-550 broadband field meter and isotropic Narda EF0691 probe (right) and height of measurements (left).

---

### 2.3. Kriging Interpolation Procedure

Point Kriging with no drifts (ordinary Kriging) and circle search space are used in this work to estimate the electric field magnitude in those points where measurements are not available. Thus, in this work we estimate the electric field magnitude at a point from the available measurements of the whole data set. Equation (1) shows how the interpolated value of the magnitude of the average electric field is obtained:

$$|E(k)|_{\text{interp}} = \sum_{i=1}^{N} w_i \cdot |E(i)|_{\text{measured}} \tag{1}$$

where $|E(k)|_{\text{interp}}$ is the estimated electric field magnitude of grid node $k$, $N$ is the number of neighboring data values used in the estimation (in this case the whole data set), and $|E(i)|_{\text{measured}}$ is the electric field magnitude value at location $i$ with weight $w_i$. The value of weights will sum to 1 ($\sum_{i=1}^N w_i = 1$) to make sure there is no bias. The KG interpolation method calculates $w_i$ from the spatial structure of data distribution represented by a sample variogram trying to minimize the interpolation error variance (Isaaks and Srivastava, 1989). In this work, authors employed Surfer v.16 software to compute the ordinary KG interpolation estimations and to generate the interpolated surface maps (Golden Software).

---

### 2.4. Error Estimation

Two different estimations were calculated for the error provided by the interpolation procedure. Equations (2) and (3) show the Root Mean Square Error (RMSE) and the Mean Absolute Error (MAE), respectively:

$$\text{RMSE} = \sqrt{\frac{1}{N} \sum_{k=1}^{N} \left( |E(k)|_{\text{interp}} - |E(k)|_{\text{measured}} \right)^2} \tag{2}$$

$$\text{MAE} = \frac{1}{N} \sum_{k=1}^{N} \left| |E(k)|_{\text{interp}} - |E(k)|_{\text{measured}} \right| \tag{3}$$

where both RMSE and MAE are expressed in $\text{V/m}$.

---

### 2.5. Minimization of the Number of Measurement Points

As explained above, a critical part of elaborating radio exposure maps is the number of measurements that must be carried out before the interpolation is performed. In this work two different procedures for measurement point reduction have been proposed, implemented, and assessed to know how they can be reduced without an important distortion of the obtained radio exposure map. Therefore, a percentage of the measured locations will be eliminated or set to zero depending on the considered method and RMSE and MAE values will be obtained for the interpolated maps.

---

## 3. Elimination of Less Significant Points (ELSP)

The method of Elimination of Less Significant Points (ELSP) consists of evaluating a percentage of the less significant points as if they had a value equal to $0\text{ V/m}$. Once these points are set to zero, they are considered and used during the KG interpolation procedure.

---

## 4. Geometrical Elimination of Neighbors (GEN) with Similar Electric Field Values

In the procedure of Geometrical Elimination of Neighbors (GEN) the matrix of measurement locations is traversed and those points whose electrical field value is closer to their neighboring points are eliminated. Those points, consequently, are not considered during the interpolation procedure.

The main difference with the ELSP method is that in GEN we eliminate the points from the interpolation calculations whereas for the ELSP method the points that are not considered or measured are set to $0\text{ V/m}$ and then used in the interpolation procedure. Another important difference is that ELSP will never eliminate electric field values with high levels because they are neighboring other points with high electric field levels.

---

## 5. Results

### 5.1. Indoor Measured Electromagnetic Map

Fig. 4a shows the measured electric field magnitude for each of the locations indicated in Fig. 2 and its histogram. Fig. 4a shows that the minimum value corresponds to the measuring point number 34 with a field level of $0.045\text{ V/m}$ ($5.37\ \mu\text{W/m}^2$) and the maximum value corresponds to the measuring point number 117 with a field level of $1.675\text{ V/m}$ ($7.44\text{ mW/m}^2$). The measurements have an average level of $0.340\text{ V/m}$ ($0.31\text{ mW/m}^2$) and a variance of $0.09\text{ (V/m)}^2$. In Fig. 4b the histogram for the electric field measurements is shown. From obtained data it can be perceived that almost 55% of electric field magnitude values are those under $0.22\text{ V/m}$ ($0.13\text{ mW/m}^2$).

Fig. 5 shows the spatial distribution of the electric field magnitude and the measuring mesh. The spatial distribution was obtained by using the ordinary KG interpolation implemented in Surfer as described in Section 2.3 and considering all the measuring data. From obtained results in Fig. 5, one can conclude that the highest electric field magnitude intensities are located around the routers that provide access to internet to students and workers. Also, the electric field is intense near some windows of the reading room since the external electric field can propagate with less attenuation than through the building walls. It can be observed that measured levels are well below the reference levels set in Council Recommendation 1999/519/EC and Directive 2013/35/EU for all the measured points.

> **Fig. 4.** (a) Electric field magnitude levels at each measuring point within the library reading room. Location number refers to measuring points indicated in Fig. 2. (b) Histogram with cumulative percentage of measured electric field levels.

> **Fig. 5.** KG interpolated spatial distribution of the electric field magnitude levels within the library reading room when using all the measurement points. Numbers indicate the measuring point identification and location.

---

### 5.2. Reduction of Measuring Points

#### 5.2.1. Usage of ELSP Strategy

Fig. 6 shows the KG interpolation of the electric field magnitude when different number of measuring points are used. In this case, the ELSP strategy was followed and 10%–90% of points were considered zero during the interpolation. Less significant points are fixed to zero intensity for each percentage. These points are the number of points accomplishing that percentage with lowest intensity values. Red crosses indicate the points whose electric field intensity is set to zero.

From obtained results it can be concluded that the obtained electric field exposure maps are very similar even for the cases (h) and (i) where we have set to zero 70% and 80% points, respectively. When setting to zero 90% of least significant measurements, however, the interpolation clearly degrades although it is still capable of showing the areas with highest values of electric field magnitude. Obviously when the chosen percentage sets to zero significant points with intermediate electric field values, then the areas with those intermediate values (green levels) are not well represented in the interpolated exposure map as it occurs for case (j).

However, this ELSP strategy shows that at least 70% of measuring points can be skipped during the measurement procedure provided that the areas with highest and medium electric field values are properly sampled. Therefore, before the measurements are carried out, a pre-scanning procedure should be performed in order to locate those areas with maximum and minimum values. The measurement mesh should prioritize those areas with high and medium values, skipping those areas where minimum values are sensed during the pre-scanning procedure. In this way measurement and post-processing time could be reduced up to a 70% with similar results for the obtained interpolated map.

Table 1 shows the Root Mean Square Error (RMSE) and the Mean Absolute Error (MAE) for the different interpolated exposure maps shown in Fig. 6 following the ELSP strategy. This table shows the evolution of both error indicators versus the percentage of least significant points set to zero before the KG interpolation is carried out. From obtained results one can conclude that both RMSE and MAE increase when the number of points set to zero grows. In the same way when the measuring point density is reduced then both error indicators grow. However, as expected, the error increase is not linear versus the percentage of points set to zero. In fact, for percentages higher than 40% the interpolation error grows at higher rate than the linear behavior observed for percentages between 10% and 30%. Three different areas have been considered in the room and linked to the number of sources and their location. Each area is a square of $10\text{ m} \times 10\text{ m}$ ($1\text{ Dm}^2 = 100\text{ m}^2$) centered on the Wi-Fi antenna. When there are walls near a Wi-Fi transmitter the area is smaller in that direction. Those areas have been named as Tx 1, Tx 2 and Tx 3 in Fig. 2.

It can be observed in Table 1 that near the Wi-Fi transmitters the mean values of the electric field are slightly different from the electric field mean value within the whole room, as expected. When using all the measurements for interpolation, the mean values of areas near the transmitters are higher but these values tend to level when using less points for interpolation. Also, reducing the density of points for interpolation with the ELSP strategy produces an increment of the mean values both at the whole measuring area and within the areas near the transmitters.

Fig. 7 shows the RMSE and MAE indicators for the interpolation versus the percentage of points set to zero before the KG interpolation is carried out. It can be observed a linear behavior for both RMSE and MAE when this percentage ranges from 10% to 30% but then the error grows in an exponential way with higher rates than the first linear behavior. The reason for this may be the following: when a low percentage of points are set to zero the error is not very big because in the ELSP strategy the points with lowest intensities are the first ones to be eliminated. However, as the percentage grows the error is higher because the measured intensity is not as low as previously eliminated points. Thus, the more points you set to zero, the higher the error increase rate.

> **Fig. 6.** Kriging interpolated spatial distribution of the electric field magnitude levels for different percentage of used measuring points by using the ELSP strategy. Red cross symbol indicates a point considered zero for interpolation calculations. Percentage of eliminated points are: (a) 10%, (b) 20%, (c) 30%, (d) 40%, (e) 50%, (f) 60%, (g) 70%, (h) 80%, and (i) 90%.

> **Fig. 7.** RMSE and MAE evolution versus the percentage of points set to zero in the ELSP strategy before the KG interpolation is carried out. Dotted lines indicate the linear behavior shown by RMSE and MAE for low percentages.

**Table 1.** Mean values, Root Mean Square Error (RMSE) and Mean Absolute Error (MAE) for the different interpolated exposure maps shown in Fig. 6 following the ELSP strategy.

| % Points Set to Zero | Meas. Density ($\text{pts}/\text{Dm}^2$) | Employed Points for Interpolation | Mean: Room (V/m) | Mean: Tx 1 (V/m) | Mean: Tx 2 (V/m) | Mean: Tx 3 (V/m) | RMSE (V/m) | MAE (V/m) |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **0%** | 42 | 386 | 0.348 | 0.418 | 0.491 | 0.478 | 0.000 | 0.000 |
| **10%** | 38 | 348 | 0.377 | 0.379 | 0.506 | 0.478 | 0.043 | 0.028 |
| **20%** | 33 | 309 | 0.409 | 0.471 | 0.506 | 0.501 | 0.045 | 0.031 |
| **30%** | 29 | 271 | 0.448 | 0.502 | 0.521 | 0.543 | 0.045 | 0.035 |
| **40%** | 25 | 232 | 0.496 | 0.537 | 0.521 | 0.632 | 0.049 | 0.041 |
| **50%** | 21 | 193 | 0.561 | 0.579 | 0.542 | 0.691 | 0.054 | 0.050 |
| **60%** | 17 | 155 | 0.647 | 0.687 | 0.617 | 0.809 | 0.061 | 0.058 |
| **70%** | 13 | 116 | 0.749 | 0.796 | 0.743 | 0.917 | 0.071 | 0.067 |
| **80%** | 8 | 78 | 0.866 | 0.866 | 0.806 | 0.988 | 0.091 | 0.081 |
| **90%** | 4 | 39 | 1.080 | 1.159 | 1.016 | 1.125 | 0.119 | 0.094 |

---

#### 5.2.2. Usage of GEN Strategy

Fig. 8 shows a comparison of three exposure maps obtained with KG interpolation under the following conditions:
- **(a)** With no measuring points being eliminated or set to zero (100% of measuring points).
- **(b)** Setting to zero 50% of the measuring points according to ELSP strategy.
- **(c)** Eliminating 50% of measured points by using GEN methodology.

From obtained results it can be observed that maps 8a and 8b show very similar electric field spatial distributions with slight differences for some areas with electric field levels around $0.7\text{ V/m}$. However, map 8c, obtained with the GEN strategy, shows a great exposure map distortion, mainly inside the black dotted line square.

This happens because the GEN approach eliminates those points whose neighbors have the closest electric field values. Since there are many points in the measuring mesh with low electric field intensity values, many of them are eliminated and, therefore, the interpolation tends to provide electric field intensities higher than the real ones.

In fact, when comparing the points set to zero in map 8b and the ones that are not considered in map 8c one can deduce that too many points have been eliminated in the distorted area of map 8c. Since this loss of information occurs with higher electric field levels around the eliminated points, then the interpolation produces an overestimation of the electric field.

Table 2 shows the RMSE and MAE for GEN and ELSP strategies, both using just 50% of real measurement points for the KG interpolation procedure. As expected from Fig. 8, it can be observed that GEN strategy for point reduction produces an interpolated map with a RMSE value almost 3 times greater than the RMSE value provided by the obtained map after the ELSP strategy. In concordance, the MAE value after the GEN strategy is twice higher than the one obtained with the ELSP approach.

> **Fig. 8.** Comparison of three electric field exposure maps obtained with KG interpolation: (a) using 100% of measuring points, (b) reduction of 50% of points by using ELSP strategy, (c) reduction of 50% of points by using GEN strategy.

**Table 2.** Root Mean Square Error (RMSE) and Mean Absolute Error (MAE) for the obtained maps using GEN and ELSP strategies for the reduction of measuring points. Both strategies use 50% of real measuring points to generate the exposure map.

| % Points Eliminated (GEN) or Set to Zero (ELSP) | Point Reduction Strategy | Employed Points for Interpolation | RMSE (V/m) | MAE (V/m) |
| :---: | :---: | :---: | :---: | :---: |
| **50%** | GEN | 193 | 0.184 | 0.103 |
| **50%** | ELSP | 193 (non-zero) | 0.066 | 0.050 |

---

## 6. Discussion

In this work it has been shown that KG interpolation technique can be applied to create an indoor exposure map for the average electric field magnitude inside a university library with routers working in the 2.45 GHz band. A quasi-regular mesh for measuring points has been used with a measuring density around $0.418\text{ measurements/m}^2$. The obtained exposure map showed that all the electric field levels were below the limits set by international standards (Council Recommendation 1999/519/EC; Directive 2013/35/EU).

The obtained results in Figs. 4 and 5 show that, as it usually happens with exposure maps, electric field levels are much higher at those areas close to the radiation sources (routers in this case), whereas the rest of areas show electric fields near $0\text{ V/m}$. In fact, the histogram in Fig. 4b shows that electric field intensities ranging from $0.07\text{ to }0.52\text{ V/m}$ provide a cumulative probability around 70%, being therefore the most frequent values as perceived in Figs. 4 and 5.

Obtaining such a measurement required 38.6 h distributed in a three-day measurement period and, therefore, it is obvious that there is a need for measurement point reduction procedures maintaining, at the same time, the accuracy and validity of the exposure map when interpolating. In this work, we have proposed and assessed two different strategies to reduce measuring points before the KG interpolation is carried out: ELSP and GEN.

Fig. 6 shows that ELSP strategy can reduce up to 70% the measurement points versus a regular mesh strategy. In this strategy, we set to zero those low intensity electric field points where measurements are not considered, and this approach provides very similar exposure maps to those ones generated with a regular mesh and produces low RMSE and MAE values. The reason for this success is that most electric field levels (around 70% as indicated by the histogram in Fig. 4b) are close to zero.

The usage of GEN procedure, however, is not successful and provides much higher RMSE and MAE errors than ELSP. In fact, this methodology is not even capable of reducing measurement points to a half as shown both in Fig. 8 and Table 2. The problem of this strategy is that it eliminates those points like their neighbors in terms of electric field magnitude. Since the majority of points have low intensity values, GEN method will overestimate the electric field in those areas where low intensity values are close to mid or high intensity values.

It is also important to mention that results have been obtained for the site under consideration, and the instrumentation used for the acquisition of E-field data described in the previous sections. Less than 20% of the obtained values are below the detection threshold and maximum values are way above it. This point should be considered for measurements taken with an instrumentation with a different detection threshold or a scenario with a different number of emitting sources and powers.

### Procedure for the Reduction of Points in Indoor Exposure Maps

From obtained results it seems clear that measurements in non-regular meshes can be used for the reduction of points in indoor electric-field radio maps without a degradation of the interpolated map. However, the measurement mesh should be finer in those areas where the electric field magnitude shows higher levels. Therefore, the following procedure could be followed to reduce the number of measuring points that allow the representation of an accurate exposure map according to ELSP strategy:

1. **Perform a fast scan** of the indoor facilities and locate those areas where maximum levels are detected.
2. **Definition of maximum level** inside the indoor area to be measured.
3. **Creation of a regular mesh** that includes those areas with maximum electric field levels.
4. **Travelling along the regular mesh** and avoiding measurements at points with an electric field level less than 30% of the maximum field level measured. Therefore, only those points whose electric field level is higher than this established threshold level shall be fully measured.
5. **Assigning a zero level** to those points in the regular mesh where measurements are not performed.
6. **Calculating the interpolation** of measured data according to the desired interpolation method.

---

## 7. Conclusion

In this work, authors have applied for the first time the KG interpolation procedure to create an electric field indoor exposure map. This map has been conducted inside a reading room of the library of the Telecommunication Engineering School at Universidad Politécnica de Cartagena. The obtained results along a regular mesh with 386 measurements indicate that electric field levels are well below the established limits in international standards and that most measured levels are close to $0\text{ V/m}$.

Two different procedures for the reduction of measuring points have been presented and evaluated: ELSP and GEN. During ELSP evaluation, those points of the regular mesh with lower electric field magnitude values are set to zero and then KG interpolation is carried out. The results indicate that up to 70% of the regular mesh can be eliminated without a noticeable distortion of the generated map.

GEN, however, eliminates the percentage of points whose neighbors have the most similar electric field levels and these eliminated points are not considered for the KG interpolation. The results show that the interpolated maps after applying GEN methodology are distorted even when eliminating only 50% of the points in the regular mesh with much greater RMSE and MAE values than ELSP strategy.

Therefore, a procedure for discarding measuring points of a regular mesh based on the results obtained in this work is provided by using the ELSP strategy. This could save time for elaborating exposure maps while maintaining map accuracy. Although these conclusions have been obtained for indoor conditions, they could be extrapolated or adapted to outdoor radio and exposure maps provided that the histograms of measurements are similar to the ones shown here.

---

## CRediT Author Statement

- **A. Martínez-González:** Resources, Supervision, Project administration, Methodology, Writing – review & editing.
- **J. Monzó-Cabrera:** Writing – review & editing, Visualization.
- **A.J. Martínez-Sáez:** Data curation, Conceptualization, Validation.
- **A.J. Lozano-Guerrero:** Investigation, Writing – review & editing.

---

## Declaration of Competing Interest

The authors declare that they have no known competing financial interests or personal relationships that could have appeared to influence the work reported in this paper.

---

## Acknowledgment

This research did not receive any specific grant from funding agencies in the public, commercial, or not-for-profit sectors.

---

## References

1. **Azpurua, M., & Dos Ramos, K. (2010).** A comparison of spatial interpolation methods for estimation of average electromagnetic field magnitude. *Progress In Electromagnetics Research M*, 14, 135–145. https://doi.org/10.2528/PIERM10083103
2. **Cleveland, R. F., & Ulcek, J. L. (1999).** *Questions and answers about biological effects and potential hazards of radiofrequency electromagnetic fields* (OET Bulletin 56, 4th ed.). Office of Engineering and Technology, Federal Communications Commission (FCC).
3. **de Andrade, H. D., de Figueiredo, A. L., Fialho, B. R. da S., Paiva, J. L. de S., Queiroz Júnior, I. S., & Sousa, M. E. T. (2020).** Analysis and development of an electromagnetic exposure map based in spatial interpolation. *Electronics Letters*, 56(8), 373–375. https://doi.org/10.1049/el.2019.3872
4. **Directive 2013/35/EU** of the European Parliament and of the Council of 26 June 2013 on the minimum health and safety requirements regarding the exposure of workers to the risks arising from physical agents (electromagnetic fields). *Official Journal of the European Union*, L 179, 1–21.
5. **EN 50413:2019.** *Basic standard on measurement and calculation procedures for human exposure to electric, magnetic and electromagnetic fields (0 Hz - 300 GHz)*. CENELEC.
6. **Garcia-Moreta, C. E., Camana-Acosta, M. R., & Koo, I. (2019).** Prediction of digital terrestrial television coverage using machine learning regression. *IEEE Transactions on Broadcasting*, 65(4), 702–712. https://doi.org/10.1109/TBC.2019.2924424
7. **Golden Software.** *Surfer: 2D & 3D mapping, modeling & analysis software*. Golden Software LLC. https://www.goldensoftware.com/products/surfer (Accessed 30 October 2021).
8. **Gonzalez-Rubio, J., Najera, A., & Arribas, E. (2016).** Comprehensive personal RF-EMF exposure map and its potential use in epidemiological studies. *Environmental Research*, 149, 105–112. https://doi.org/10.1016/j.envres.2016.05.008
9. **ICNIRP (1998).** Guidelines for limiting exposure to time-varying electric, magnetic, and electromagnetic fields (up to 300 GHz). *Health Physics*, 74(4), 494–522.
10. **ICNIRP (2020).** Guidelines for limiting exposure to electromagnetic fields (100 kHz to 300 GHz). *Health Physics*, 118(5), 483–524. https://doi.org/10.1097/HP.0000000000001210
11. **IEEE (2019).** *IEEE Standard for Safety Levels with Respect to Human Exposure to Electric, Magnetic, and Electromagnetic Fields, 0 Hz to 300 GHz* (IEEE Std C95.1-2019). IEEE. https://doi.org/10.1109/IEEESTD.2019.8930419
12. **Isaaks, E. H., & Srivastava, R. M. (1989).** *An Introduction to Applied Geostatistics* (pp. 278–322). Oxford University Press, New York.
13. **ITU-T Recommendation K.100 (2014).** *Measurement of radio frequency electromagnetic fields to determine compliance with human exposure limits when a base station is put into service*. International Telecommunication Union.
14. **Nájera, A., Ramírez-Vázquez, R., Arribas, E., & González-Rubio, J. (2020).** Comparison of statistic methods for censored personal exposure to RF-EMF data. *Environmental Monitoring and Assessment*, 192, 77. https://doi.org/10.1007/s10661-019-8021-z
15. **Ould Isselmou, Y., Wackernagel, H., Tabbara, W., & Wiart, J. (2006).** Geostatistical interpolation for mapping radio-electric exposure levels. In *Proceedings of the First European Conference on Antennas and Propagation (EuCAP 2006)* (pp. 1–6). Nice, France. https://doi.org/10.1109/EUCAP.2006.4588970
16. **Paniagua, J. M., Rufo, M., Jiménez, A., Silva, A. C., & Antolín, A. (2013).** The spatial statistics formalism applied to mapping electromagnetic radiation in urban areas. *Environmental Monitoring and Assessment*, 185(1), 311–322. https://doi.org/10.1007/s10661-012-2555-7
17. **Pinheiro, F., Maranhão, T., Bernardo-Filho, M., et al. (2015).** Assessment of non-ionizing radiation from radio frequency energy emitters in the urban area of Natal city, Brazil. *Scientific Research and Essays*, 10(2), 79–85. https://doi.org/10.5897/SRE2014.6025
18. **Recommendation 1999/519/EC.** Council Recommendation of 12 July 1999 on the limitation of exposure of the general public to electromagnetic fields (0 Hz to 300 GHz). *Official Journal of the European Communities*, L 199, 59–70.
19. **Röösli, M., Frei, P., Mohler, E., Braun-Fahrländer, C., Bürgi, A., Fröhlich, J., Neubauer, G., Theis, G., & Egger, M. (2008).** Statistical analysis of personal radiofrequency electromagnetic field measurements with nondetects. *Bioelectromagnetics*, 29(6), 471–478. https://doi.org/10.1002/bem.20417
20. **Röösli, M., Frei, P., Mohler, E., & Hug, K. (2010).** Systematic review on the health effects of exposure to radiofrequency electromagnetic fields from mobile phone base stations. *Bulletin of the World Health Organization*, 88(12), 887–896. https://doi.org/10.2471/BLT.09.071852
21. **Rufo, M., Antolín, A., Paniagua, J. M., & Jiménez, A. (2018).** Optimization and comparison of three spatial interpolation methods for electromagnetic levels in the AM band within an urban area. *Environmental Research*, 162, 219–225. https://doi.org/10.1016/j.envres.2017.12.029
22. **Safigianni, A. S., & Tsompanidou, C. G. (2009).** Electric- and magnetic-field measurements in an outdoor electric power substation. *IEEE Transactions on Power Delivery*, 24(1), 38–42. https://doi.org/10.1109/TPWRD.2008.2005680
23. **Santana, T. A. A., de Andrade, H. D., Queiroz Júnior, I. S., & Tavares da Silva, I. B. (2017).** Comparison of spatial interpolation methods to determine exposure ratio to electric fields in urban environments. *Electronics Letters*, 53(18), 1250–1252. https://doi.org/10.1049/el.2017.1517
24. **Sato, K., & Fujii, T. (2017).** Kriging-based interference power constraint: integrated design of the radio environment map and transmission power. *IEEE Transactions on Cognitive Communications and Networking*, 3(1), 13–25. https://doi.org/10.1109/TCCN.2017.2688410
25. **Sato, K., Inage, K., & Fujii, T. (2019).** Modeling the Kriging-aided spatial spectrum sharing over log-normal channels. *IEEE Wireless Communications Letters*, 8(3), 749–752. https://doi.org/10.1109/LWC.2019.2891637
26. **Sato, K., Suto, K., Inage, K., Adachi, K., & Fujii, T. (2021).** Space-frequency-interpolated radio map. *IEEE Transactions on Vehicular Technology*, 70(1), 714–724. https://doi.org/10.1109/TVT.2020.3048994
27. **Shan, J., Shao, W., Xue, H., Xu, Y., Mao, D., 2018.** The method of electromagnetic environment map construction based on Kriging spatial interpolation. In *Proceedings of the 2018 International Conference on Information Systems and Computer Aided Education (ICISCAE)* (pp. 212–217). IEEE, Changchun, China. https://doi.org/10.1109/ICISCAE.2018.8666993
28. **Solin, A., Kok, M., Wahlström, N., Schön, T. B., & Särkkä, S. (2018).** Modeling and interpolation of the ambient magnetic field by Gaussian processes. *IEEE Transactions on Robotics*, 34(4), 1112–1127. https://doi.org/10.1109/TRO.2018.2830410
