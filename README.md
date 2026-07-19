# Chennai Urban Heat Mitigation — Results One-Pager
*(All values measured from the pipeline; use directly in the report.)*

## Study design
- AOI: Greater Chennai, 80.05–80.35 E / 12.85–13.35 N, 30 m grid (1848 × 1091)
- Data: Landsat 8/9 C2L2 LST + SR (Mar–Jun 2019–2025 composite), ESA WorldCover 10 m,
  GHSL (built surface / height / volume / population), SRTM DEM
- Valid pixels 2,001,080; land after water mask 1,567,833 (water = 21.7 % of AOI)
- Land mean LST 41.5 °C vs sea 29.8 °C (gap 11.7 °C); airport zone 44.1 °C; Guindy NP 40.6 °C

## Key methodological findings (report these — they are results)
1. **Simpson's paradox from water contamination**: corr(LST, NDVI) = +0.506 with water
   included → −0.600 land-only; NDBI −0.359 → +0.762; NDWI −0.803 → +0.461.
   An uncorrected model is a water detector (R² 0.94 → honest 0.68).
2. **Albedo is confounded** (bright = dry bare ground): unconstrained ML can predict
   cool roofs *heat* the city. Fixed with monotone constraints
   (∂LST/∂albedo ≤ 0, ∂LST/∂NDVI ≤ 0, tree/lowveg/wetland ≤ 0).
3. **Naive Getis-Ord Gi\* flags 79.2 % of land** "significant" (autocorrelation).
   Phase-randomised surrogate null (Theiler et al. 1992): null z-std 7.88 vs observed
   7.94 → corrected hotspots **3.06 % of land (43.2 km²) @95 %, 0.77 % (10.9 km²) @99 %**.
4. **GHS-POP resampling must conserve mass**: 100 m→30 m without rescale inflates
   population 11.1× ( (100/30)² ). Corrected with POP_SCALE = 0.09.

## Model
- XGBoost, 15 features, spatial block CV (3 km blocks, GroupKFold-5)
- Unconstrained: R² 0.675 ± 0.011, RMSE 1.40 °C · Constrained: R² 0.642 (sign-guaranteed)
- Random-pixel CV inflates to R² ≈ 0.9+ (leakage demo — worth one slide)

## Drivers (mean |SHAP|, °C, land-only constrained model)
NDBI 0.971 · NDVI 0.384 · building height 0.263 · dist-to-coast 0.241 ·
dist-to-water 0.172 · albedo 0.132 · elevation 0.129 · tree fraction 0.129

## UTFVI thermal comfort
Excellent 43.9 % (619 km²) · Worst 40.2 % (567 km²) → the city is thermally polarised.

## Scenarios (physics-constrained; albedo capped at 0.45 = in-range)
| Scenario | Treated | ΔLST (treated mean) | People (corrected) |
|---|---|---|---|
| S1 Cool roofs | 526.6 km² | −0.43 °C | 9.47 M |
| S2 Urban greening | 458.4 km² | −1.09 °C | 8.32 M |
| S3 Ennore wetland | 2.6 km² | −1.04 °C | 3.9 k |
| S4 Airport pavement | 3.6 km² | −1.14 °C | 11.8 k |
| S5 Combined | 552.5 km² | −1.65 °C | 9.59 M |

Zone response under S5: Airport −4.04 °C · Ennore −2.61 · T.Nagar −1.66 · Perungudi −1.51.

## Optimisation (300 m cells; ₹250/m² roofs, ₹2,500/tree @150/ha; NSGA-II 3-objective)
- Greedy frontier: ₹50 cr → 2.1 M person-°C · ₹100 → 3.6 · ₹250 → 6.2 · ₹500 → 8.0 (saturation)
- Recommended plan: 819 cells (807 greening / 9 both / 3 roofs), ₹62.8 cr,
  2.53 M person-°C, 0.23 M inside Tier-1 hotspots
- **Headline: greening saturates eligible land before budget binds** — green first,
  coat roofs only in the unplantable core. (Sensitive to cost assumptions; state them.)

## Aerospace module (first-order, stated couplings)
Apron ΔLST −1.14 °C → ΔT_air ≈ −0.46 °C (coupling 0.4) → **≈55 ft density altitude
recovered**, ≈0.5 % shorter takeoff ground roll on hot afternoons.
Orbital-mechanics framing: Landsat = sun-synchronous 10:30 fixed sampling; ECOSTRESS on
ISS (51.6°, precessing) samples the diurnal cycle; pipeline is TRISHNA-ready (~57 m TIR).

## Stated limitations
LST ≠ 2 m air temperature (morning overpass) · constraints guarantee sign not magnitude
(cross-check InVEST/SOLWEIG in pilot wards) · costs are order-of-magnitude ·
NSGA candidate cap ≈₹65 cr (raise N_CAND for full-axis front) · ECOSTRESS night
extension pending.
