name: Build Project Matrixx GSI for A05s

on:
  workflow_dispatch:

jobs:
  build-gsi:
    runs-on: ubuntu-latest
    timeout-minutes: 360  # max 6 hours
    steps:
      - name: Checkout Repo
        uses: actions/checkout@v3

      - name: Install Dependencies
        run: |
          sudo apt update
          sudo apt install -y git curl repo openjdk-11-jdk bc bison build-essential flex \
            libncurses5-dev libssl-dev libxml2-utils lzop pngcrush rsync schedtool unzip zip zlib1g-dev

      - name: Setup Repo and Sync Project Matrixx
        run: |
          mkdir -p ~/matrixx_gsi
          cd ~/matrixx_gsi
          repo init -u https://github.com/ProjectMatrixx/manifest.git -b main
          repo sync -c -j4

      - name: Setup Build Environment
        run: |
          cd ~/matrixx_gsi
          source build/envsetup.sh
          export USE_CCACHE=1
          export CCACHE_DIR=~/matrixx_gsi_ccache
          ccache -M 20G

      - name: Add Device Tree, Kernel, Vendor
        run: |
          cd ~/matrixx_gsi
          git clone https://github.com/username/device_samsung_a05s.git device/samsung/a05s
          git clone https://github.com/username/kernel_samsung_a05s.git kernel/samsung/a05s
          # Ensure vendor blobs are inside device/samsung/a05s/vendor

      - name: Lunch Target
        run: |
          cd ~/matrixx_gsi
          lunch aosp_arm64_ab-userdebug  # AB Treble target

      - name: Build System Image
        run: |
          cd ~/matrixx_gsi
          mka systemimage -j4

      - name: Upload GSI Artifact
        uses: actions/upload-artifact@v3
        with:
          name: ProjectMatrixx-A05s-GSI
          path: ~/matrixx_gsi/out/target/product/generic_arm64_ab/system.img
