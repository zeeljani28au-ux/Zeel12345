# Week 08 – Cloud Computing

| | |
|---|---|
| **Student** | Zeel Pareshbhai Jani (12331098) |
| **Tutor** | Dr David Ling |

> **TODO – this week's lab results are not filled in yet.** Every `TODO` below must be replaced with your own lab output before submitting. Search the file for `TODO`.

## Task 1 – Knowledge Test

Completed the Week 8 Knowledge Test within the first 10 minutes of the tutorial.

## Task 2 – Login to Microsoft Learn on Demand

Registered on <https://msle.learnondemand.net/> with my `@cqumail.com` address and the training key from Moodle to create a Skillable account, then signed in with **Sign In → Skillable Account** and entered the class `COIT20246` (Microsoft Azure Fundamentals).

> **TODO:** add a screenshot of the Learn on Demand dashboard showing the class, e.g. `week08/week8-task2-dashboard.png`.

## Task 3 – Create an Azure Resource (Module 01)

Resources created in the lab and what each one is for:

| Resource (name from lab) | Resource type | What it is for |
|---|---|---|
| TODO | TODO | TODO |

> **TODO:** list every resource the lab created (Azure Portal → *All resources*). Use the lab's own resource names.

## Task 4 – Create an Azure Virtual Machine and Allow Web Access (Module 02)

### `az` commands used to create the VM and install Nginx

```sh
# TODO: paste the exact az commands from your lab (text, not a screenshot)
```

### Public IP address of the VM

`TODO`

### Steps to allow web access

1. Browsed to `http://<public-ip>` – **connection timed out**, because the Network Security Group only allowed SSH (port 22).
2. In the Azure Portal: *All resources → Network Security Group → Inbound security rules → Add*, chose **HTTP** as the service and added the rule.
3. Browsed to `http://<public-ip>` again – the website now loaded.
4. Logged in to the Ubuntu VM from Cloud Shell and edited the web page:

   ```sh
   ssh -l azureuser <public-ip>
   sudo nano /var/www/html/index.html
   ```

5. Added my name to the HTML, saved with Ctrl-O, exited with Ctrl-X and refreshed the browser – my name appeared.

> **TODO:** insert a screenshot of the browser showing the website **with your name**, e.g. `week08/week8-task4-website.png`.

### Network security rules that allow access to the VM

| Rule (priority) | Port | Protocol | What it allows |
|---|---|---|---|
| `default-allow-ssh` (1000) | 22 | TCP | **SSH** – secure remote command-line login and administration of the Ubuntu VM (e.g. from Cloud Shell). |
| `AllowAnyHTTPInbound` (1010) | 80 | TCP | **HTTP** – web browsers can reach the Nginx web server and load the website. |

## Task 5 – Compare Cloud vs On-premise Costs

**Tutor's specification limits for my class:** `TODO` (e.g. RAM between X GB and Y GB)

| | Consumer desktop PC | Azure virtual machine |
|---|---|---|
| Product / size | TODO | TODO |
| CPU | TODO | TODO (vCPU) |
| RAM | TODO | TODO |
| Storage | TODO | TODO |
| Operating system | TODO | TODO |
| Upfront cost (AUD) | TODO | TODO |
| Running cost per month (AUD) | TODO | TODO |
| **1-year cost (AUD)** | TODO | TODO |
| **3-year cost (AUD)** | TODO | TODO |

- PC prices from an Australian online shop: TODO (shop + link)
- Azure prices from the Azure pricing calculator (region, size, OS, hours): TODO

> **TODO:** insert a screenshot of the consumer PC price (`week08/week8-task5-pc-price.png`) and an export/screenshot from the Azure pricing calculator (`week08/week8-task5-azure-calculator.png`).

### Trade-offs

> **TODO:** discuss upfront vs running cost over 1 and 3 years, flexibility/scaling, maintenance, power/hardware failure, security/control, and availability (advantages and disadvantages of each option).

## Task 6 – Create a Storage Blob in Azure (optional)

> **TODO (optional):** screenshot showing an image and its full URL, plus a screenshot of the Azure Portal resources showing the container(s). If anonymous access is blocked, enable *Allow Blob anonymous access* under the storage account's *Configuration* (Settings) and then set the container access level to *Blob*.

## Task 7 – Create a Resource Lock (optional)

Difference between the two lock types:

- **Delete lock** – users can still read and modify the resource, but nobody can delete it until the lock is removed.
- **Read-only lock** – the resource can be viewed but not changed or deleted; it effectively makes the resource read-only for everyone, even users with Owner/Contributor permissions.
