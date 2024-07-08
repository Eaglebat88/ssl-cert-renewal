mkdir -p .github/workflows
.github/workflows/renew-ssl.yml
name: Renew SSL Certificate

on:
  schedule:
    - cron: '0 0 27 */3 *'  # Runs every 3 months on the 27th day at midnight
  workflow_dispatch:  # Allows manual triggering

jobs:
  renew-ssl:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v2

    - name: Install curl
      run: sudo apt-get install -y curl

    - name: Renew SSL Certificate
      env:
        API_KEY: ${{ secrets.API_KEY }}
      run: |
        cat <<EOF > renew_ssl.json
        {
          "apikey": "${{ secrets.API_KEY }}",
          "domain": "padyak2024.work.gd",
          "action": "renew",
          "verbose": "true"
        }
        EOF
        curl -H "Content-Type: application/json" --data @renew_ssl.json https://api.dnsexit.com/dns/lse.jsp
