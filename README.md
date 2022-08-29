# lnd-chart
lnd helm chart

Local install:

helm upgrade --install -n lnd lnd chart/lnd --create-namespace --values chart/lnd/values.yaml

helm upgrade --install bitcoind bitcoind/bitcoind -n bitcoind --version 0.1.0 --create-namespace
