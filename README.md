# 📦 AWS EBS Snapshot (Backup & Restore) – Step‑by‑Step Guide

This guide explains **how to take an EBS snapshot (backup)** from an EC2 instance and **restore it to a new backup instance**, with verification steps.

---

## 🔹 Prerequisites

* An existing **EC2 instance** with an attached **EBS volume**
* SSH access to the instance
* AWS Console access
* Key pair available

---

## 🟢 Step 1: Create File & Folder on EC2 Instance

1. **Connect to your EC2 instance** (AWS Linux) using SSH.
2. Run the following commands:

```bash
ls
pwd

# Create folder
mkdir myfolder
ls

# Go inside folder
cd myfolder/

# Create file
touch myfile
ls

# Write data into file
echo hello > myfile

# Verify file content
cat myfile
```

✅ This data will be used to verify the snapshot restore.

---

## 🟢 Step 2: Create Snapshot of EBS Volume

1. Go to **AWS EC2 Dashboard**
2. Click **Volumes**
3. Select the EBS volume attached to your instance
4. Click **Actions → Create snapshot**
5. Enter:

   * **Description:** `test snapshot`
6. Click **Create snapshot**

---

## 🟢 Step 3: Check Snapshot Status

1. Go to **Snapshots**
2. Verify **Status = Completed**

---

## 🟢 Step 4: Create New Volume from Snapshot

1. Select the snapshot
2. Click **Actions → Create  volume from snapshot **
3. Choose:

   * **Availability Zone:** Same AZ where backup instance will be created
4. Click **Create volume**

---

## 🟢 Step 5: Verify New Volume

1. Go to **Volumes**
2. Check:

   * **State:** Available
   * **Availability Zone:** Correct

---

## 🟢 Step 6: Launch Backup EC2 Instance

1. Go to **Instances → Launch Instance**
2. Enter:

   * **Name:** `MYbackupserver`
3. Select:

   * Amazon Linux AMI
   * Instance type (as required)
   * Key pair
4. **Network Settings → Edit**

   * Select **Availability Zone** (same as snapshot volume)
   * Select subnet from same AZ
   * Enable **HTTP** (if required)
5. Click **Launch Instance**

---

## 🟢 Step 7: Attach Snapshot Volume to Backup Instance

1. Go to **Volumes**
2. Select the volume created from snapshot
3. Click **Actions → Attach volume**
4. Select:

   * **Instance:** MYbackupserver
   * **Device name:** `/dev/sdb`
5. Click **Attach volume**

---

## 🟢 Step 8: Mount Volume on Backup Instance

1. SSH into **MYbackupserver**
2. Run:

```bash
lsblk
```

Expected output:

* Device shown as `nvme1n1`

3. Check filesystem:

```bash
sudo file -s /dev/nvme1n1p1
```

4. Create mount directory:

```bash
sudo mkdir /mnt/mybackup
```

5. Mount volume:

```bash
sudo mount -o nouuid /dev/nvme1n1p1 /mnt/mybackup/
```

6. Verify mount:

```bash
df -h
```

---

## 🟢 Step 9: Verify Backup Data

```bash
ls
cd /mnt/mybackup/home/ec2-user/myfolder/
cat myfile
```

✅ Output should be:

```
hello
```

This confirms the **EBS snapshot backup and restore** was successful.

---

## 🎯 Conclusion

* EBS snapshots are **point‑in‑time backups**
* Snapshots can be restored to **new volumes**
* Data remains safe even if the original EC2 instance is terminated

---

## 📌 Best Practices

* Take snapshots regularly
* Use meaningful snapshot descriptions
* Keep snapshot and instance in same Availability Zone

---

## 👨‍💻 Author

  Kumlesh Kurre
💼 IT Support & Network Engineer

⭐ If you find this guide helpful, don’t forget to star ⭐ the GitHub repository

Purpose: AWS Learning & Practice 🚀
