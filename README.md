Sample spring application


# Docs for the Azure Web Apps Deploy action: https://github.com/azure/functions-action
# More GitHub Actions for Azure: https://github.com/Azure/actions

name: Build and deploy Java project to Azure Function App - tringer3

on:
  push:
    branches:
      - main
  workflow_dispatch:

env:
  AZURE_FUNCTIONAPP_NAME: tringer3 # set this to your function app name on Azure
  PACKAGE_DIRECTORY: '.' # set this to the directory which contains pom.xml file
  JAVA_VERSION: '17' # set this to the java version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    
    steps:
      - name: 'Checkout GitHub Action'
        uses: actions/checkout@v4

      - name: Setup Java Sdk ${{ env.JAVA_VERSION }}
        uses: actions/setup-java@v4
        with:
          java-version: ${{ env.JAVA_VERSION }}
          distribution: 'microsoft'

      - name: 'Restore Project Dependencies Using Mvn'
        shell: pwsh
        run: |
          pushd './${{ env.PACKAGE_DIRECTORY }}'
          mvn clean package
          popd
      
      - name: 'Run Azure Functions Action'
        uses: Azure/functions-action@v1
        id: fa
        with:
          app-name: 'tringer3'
          slot-name: 'production'
          package: '${{ env.PACKAGE_DIRECTORY }}'
          respect-pom-xml: true
          publish-profile: ${{ secrets.AzureAppService_PublishProfile_a67c2f91109d46a9bfc8da859fa28ac7 }}
