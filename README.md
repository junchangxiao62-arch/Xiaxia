name: Build Xiao AI APK

on:
  workflow_dispatch:

permissions:
  contents: read

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 22

      - name: Set up Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: 21

      - name: Install Capacitor
        run: |
          mkdir -p www
          cp index.html www/index.html
          npm init -y
          npm install @capacitor/core @capacitor/android
          npm install --save-dev @capacitor/cli

      - name: Configure Capacitor
        run: |
          npx cap init "Xiao AI" "com.xiaoai.chat" --web-dir=www
          npx cap add android

      - name: Build APK
        run: |
          cd android
          chmod +x gradlew
          ./gradlew assembleDebug

      - name: Upload APK
        uses: actions/upload-artifact@v4
        with:
          name: Xiao-AI-APK
          path: android/app/build/outputs/apk/debug/app-debug.apk
          if-no-files-found: error
          retention-days: 7
