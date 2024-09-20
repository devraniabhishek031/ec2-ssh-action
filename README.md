name: Deploy to EC2

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Update package list and install OpenSSH Server on the runner
        run: |
          sudo apt-get update
          sudo apt-get install -y openssh-server
      - name: Login to EC2
        uses: devraniabhishek031/ec2-ssh-action@v1.0.0
        with:
          EC2_HOST: ${{ secrets.EC2_HOST }}
          EC2_USERNAME: ${{ secrets.EC2_USERNAME }}
          EC2_PASSWORD: ${{ secrets.EC2_PASSWORD }}

      - name: Create a new file on the EC2 instance with "hello-world"
        run: |
          sshpass -p "${{ secrets.EC2_PASSWORD }}" ssh -t -o StrictHostKeyChecking=no ${{ secrets.EC2_USERNAME }}@${{ secrets.EC2_HOST }} "echo 'hello-world' >> new_file.txt"
     
