# Creating a Custom Nerves System for ARK1124H

The generic `nerves_system_x86_64` is missing Intel Bay Trail-specific drivers needed for the ARK1124H (Intel Atom E3825).

## Required Steps

1. **Fork/clone nerves_system_x86_64:**
   ```bash
   git clone https://github.com/nerves-project/nerves_system_x86_64.git
   cd nerves_system_x86_64
   ```

2. **Add Intel Bay Trail drivers to kernel config:**
   
   Edit `linux-6.12.defconfig` and add:
   ```
   # Intel Graphics (required for Bay Trail)
   CONFIG_DRM_I915=y
   CONFIG_DRM_I915_FORCE_PROBE=""
   
   # Intel Chipset support
   CONFIG_X86_INTEL_LPSS=y
   CONFIG_MFD_INTEL_LPSS=y
   CONFIG_MFD_INTEL_LPSS_ACPI=y
   
   # Intel ACPI
   CONFIG_ACPI=y
   CONFIG_ACPI_AC=y
   CONFIG_ACPI_BATTERY=y
   CONFIG_ACPI_BUTTON=y
   CONFIG_ACPI_FAN=y
   CONFIG_ACPI_PROCESSOR=y
   CONFIG_ACPI_THERMAL=y
   
   # Intel Ethernet (if needed)
   CONFIG_E1000=y
   CONFIG_E1000E=y
   CONFIG_IGB=y
   
   # Intel Audio (if needed)
   CONFIG_SND_HDA_INTEL=y
   ```

3. **Update mix.exs to use your custom system:**
   
   In your project's `mix.exs`, change:
   ```elixir
   {:nerves_system_x86_64, path: "../nerves_system_x86_64", runtime: false, targets: :x86_64}
   ```

4. **Build the custom system:**
   ```bash
   cd nerves_system_x86_64
   MIX_TARGET=x86_64 mix deps.get
   MIX_TARGET=x86_64 mix firmware
   ```

5. **Build your application:**
   ```bash
   cd ../impala-advantech-ark1124h
   MIX_TARGET=x86_64 mix deps.get
   MIX_TARGET=x86_64 mix firmware
   ```

## Alternative: Quick Test with Minimal Changes

If you want to test quickly, you can try adding kernel boot parameters to disable problematic features:

Add to GRUB (if we can modify it):
```
i915.modeset=0 acpi=off
```

But this is not recommended for production.

