WAN IP Checker

This Python script monitors your public (WAN) IP address and exposes it as a Prometheus metric for monitoring in Grafana. It can be used to track changes in your IP over time and trigger alerts if it changes.

Features

Checks your current public IP from a reliable web service.

Compares the current IP with the last known IP.

Writes the result as a Prometheus-compatible .prom file (wan_ip.prom) for node_exporter textfile collector.

Marks whether the IP has changed (wan_ip_change = 1) or stayed the same (wan_ip_change = 0).

Stores the last known IP in last_ip.txt for comparison on the next run.

Can be scheduled to run periodically using cron.

How it Works

The script queries http://checkip.dyndns.com/ to determine your current public IP.

Reads the previous IP from last_ip.txt (created on first run if missing).

If the current IP is different from the last recorded IP, it sets wan_ip_change to 1 and updates last_ip.txt.

If the IP is unchanged, it sets wan_ip_change to 0.

The .prom file is written in the directory monitored by node_exporter’s textfile collector. Prometheus can scrape this metric, and Grafana can visualize it or trigger alerts.

Usage

Clone the repository to your server:

git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>


Ensure you have Python 3 installed.

Run the script manually to test:

python3 update_wan_ip.py


Configure the node_exporter container (or host installation) to include a textfile collector that points to the directory where wan_ip.prom is written. For example:

/root/wan_ip_checker/textfiles


Add a cron job to run the script periodically (e.g., every 12 hours):

0 */12 * * * python3 /root/wan_ip_checker/update_wan_ip.py


Configure Prometheus to scrape node_exporter metrics, and optionally set up a Grafana alert rule to notify when wan_ip_change == 1.

When to Use

If you have a dynamic WAN IP and need to monitor when it changes.

For home labs or servers behind residential ISPs where public IP changes can affect remote access.

When you want to integrate WAN IP tracking into your Prometheus + Grafana monitoring stack.

Useful for triggering alerts or automations when your IP changes.
