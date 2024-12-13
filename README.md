# Project Overview

This repository contains the implementation and evaluation of a predictive model for video chunk download times. The project leverages datasets from Stanford's Puffer Project and Twitch live streams to highlight challenges in model generalization.

---

## Prerequisites

- **Python**: Version 3.8+
- **Required Libraries**: `numpy`, `pandas`, `scikit-learn`, `tshark`, `netunicorn`
- **Datasets**: 
  - Access to the Puffer Project dataset.
  - Tools like PINOT and netUnicorn for Twitch data collection.

---

## Setup Instructions

### 1. Clone the Repository
```bash
git clone https://github.com/caleb-stahl/Final_Project.git
```

### 2. Install Dependencies
```bash
pip install -r requirements.txt
```
---

## Data Preparation

### Puffer Project Data
1. Download the dataset from the [Puffer Project website](https://puffer.stanford.edu). To use the same data we used, download the December 4th, 2024 data. 
2. Place the dataset in the `data/puffer` directory.

### Twitch Data
1. Use [PINOT](https://github.com/pinot) and [netUnicorn](https://github.com/netUnicorn) to capture streaming data (or use the data we captured). Optionally, you can use a tool like WireShark or Tshark to get packet caputures.
2. PreProcess the data you captured with data (See below)
3. Place the packet captures in the `data/twitch` directory.

---

## Usage

### 1. Replicate Data Collection
* To replicate data collection, you must either have access to a network with PINOT and netUnicorn setup, or Wireshark/tshark on your computer. 
  * If the former is the case, open and read through the `data_collection.ipynb` notebook for guided instructions on how to define and run your own netunicorn experiment similar to ours. 
  * If the latter is the case, follow a Wireshark/tshark tutorial, such as [this one for Wireshark](https://www.varonis.com/blog/how-to-use-wireshark) or [this one for tshark](https://hackertarget.com/tshark-tutorial-and-filter-examples/), and save the pcap after you collect it. 

### 2. Preprocess the Data
After you have collected the data, the next step is to preprocess it so that we can get it into a format similar to the Puffer dataset. 
* First, we need to run the following command with `tshark` (which you should already have intalled from an earlier step): 
```bash
tshark -r file.pcap -T fields -E separator=/t -e frame.time_epoch -e ip.src -e tcp.srcport -e udp.srcport -e ip.dst -e tcp.dstport -e udp.dstport -e ip.len -e ip.hdr_len -e ip.proto -e tcp.flags -e tcp.seq_raw -e tcp.ack_raw -e tcp.hdr_len -e udp.length -e tcp.analysis.retransmission -e tcp.analysis.ack_rtt -e tcp.seq -e tcp.ack >> file.csv
```
  * This command extracts the necesary features from the PCAP which we can further mold to fit the structure of the Puffer Data set. The final features we decided to extract are:
    * Download Duration: The feature we are trying to predict (seconds). 
    * Size: The size of the video chunk (bytes)
    * RTT: Time it takes for a data packet to travel from the sender to the client and then back to the sender (nanoseconds).
    * Throughput: Rate at which data is transmitted over a network (Mbps).
    * Bytes Per Transmission Time: Size of the chunk/ RTT of the chunk
    * In Flight: Number of dropped or missing packets.
* Second, run the CSV that tshark created, following the instruction in the MarkDown cells of `data_processing.ipynb`m  to extract another CSV file containing the features above.
* Third and finally, place this CSV file in the `data/test_data/` folder of this repository. 

### 3. Train/Evaluate the Model


---

## Results

Evaluation metrics (e.g., MAE, RMSE) for both datasets are detailed in the final report.

---

## References

- **Puffer Project**: [https://puffer.stanford.edu](https://puffer.stanford.edu)
- **netUnicorn Github**: [https://github.com/netunicorn](https://github.com/netunicorn)
- **trustee Github** [https://github.com/TrusteeML/trustee](https://github.com/TrusteeML/trustee)
