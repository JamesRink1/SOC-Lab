<h1>SOC Environment with a honeypot</h1>



<h2>Description</h2>
<b>In this lab I simulate a SOC environment with a vulnerable machine and collect/analyze logs from attackers around the world. Utilizing Josh Madakors guide, I will be using Microsoft Azure to create a virtual machine (VM), exposing it to the internet as a honeypot, and forwarding logs to the Log Analytics Workspace which will be our central log repository. We then integrate Microsoft Sentinel to analyze real-world attack data.</b>


</b>Josh Madakor's Guide: https://www.youtube.com/watch?v=g5JL2RIbThM&t=3598s</b>



<h3>Step 1: We will create a resource group in Microsoft Azure to act as our folder and then create a virtual network so that we can attach our virtual machine to the internet.</h3>



<p align="center">
<img width="85%" height="85%" alt="Screenshot (26)" src="https://github.com/user-attachments/assets/6a0402f0-12eb-4e57-bdc5-1b27fbb5cb54" />




<h3>Step 2: We create a Virtual Machine with an inconspicuous name such as "CORP-NET-EAST-1". Ensure to leave the RDP port open to have remote access and to make it vulnerable to attacks.</h3>



<p align="center">
<img width="85%" height="85%" alt="Screenshot (28)" src="https://github.com/user-attachments/assets/572098fb-be97-4d6d-b879-d53ce35c86e5" />




<h3>Step 3: Next we edit the Network Security Group (NSG) by removing the current Inbound RDP rule. Then we make our custom rule named "DANGER_AllowAnyInBound", this rule will allow any traffic in from any source or port. This new rule will make our VM easily discoverable on the Internet. </h3>



<p align="center">
<img width="85%" height="85%" alt="Screenshot (31)" src="https://github.com/user-attachments/assets/58f7e34f-8619-422a-b269-368e089c3465" />





<h3>Step 4: We then RDP into the VM from our host machine and turn off Microsoft Defender Firewalls to ensure the machine is as vulnerable as possible. We also ping the VM to make sure it is reachable on the public internet.</h3>



<p align="center">
<img width="85%" height="85%" alt="Screenshot (33)" src="https://github.com/user-attachments/assets/696a8017-5b33-422a-9d1c-f9592fcaa3de" />


  
<img width="85%" height="85%" alt="Screenshot (34)" src="https://github.com/user-attachments/assets/c80b6822-3e88-49c2-800c-14a337bf361c" />



<img width="85%" height="85%" alt="Screenshot (35)" src="https://github.com/user-attachments/assets/3b8011e6-d853-4fbd-8a22-bcf0deeab3d2" />




<h3>Step 5: Next we provide a few intentional failed logins to view in EventViewer. Under the Windows logs -> Security we can verify 3 failed logins with the EventID of 4625. This will also allow you to see the source IP from the failed login attempt! </h3>



<p align="center">
<img width="85%" height="85%" alt="Screenshot (37)" src="https://github.com/user-attachments/assets/ed0b18bc-f586-4ac5-99dd-6035abe2bdf8" />


  
  
<h3>Step 6: We then create a Log Analytics Workspace(LAW) to be our central log repository.</h3>




<p align="center">
<img width="85%" height="85%" alt="Screenshot (39)" src="https://github.com/user-attachments/assets/1f61a4d3-d2f1-4086-8e80-af957d3d2272" />




<h3>Step 7: Afterwards, we create a Microsoft Sentinel instance which will act as our SIEM and link it to our Log Analytics Workspace.<h3>



<p align="center">
<img width="85%" height="85%" alt="Screenshot (41)" src="https://github.com/user-attachments/assets/1362281f-8796-4ba2-8b3b-8b9176d39866" />




<h3>Step 8: In Sentinel we will install and configure the Azure monitoring agent for Security Events connector to provide a link between the VM and our Log Analytics Workspace to receive logs into Sentinel. </h3>



<p align="center">
<img width="85%" height="85%" alt="Screenshot (42)" src="https://github.com/user-attachments/assets/cd5267e3-a498-48ac-aa75-f75cd77347a3" />

<img width="85%" height="85%" alt="Screenshot (44)" src="https://github.com/user-attachments/assets/9cff64f8-4ba9-4508-8dce-8119a4f66a51" />




<h3>Step 9: We can verify this connection in our Virtual Machine through the VM Settings -> Extensions and Applications.</h3>



<p align="center">
<img width="85%" height="85%" alt="Screenshot (47)" src="https://github.com/user-attachments/assets/b7393917-e52f-47de-937b-2ee4e66ed925" />




<h3>Step 10: In our Log Analytics Workspace under the Logs tab you can start to see logs start coming in thanks to our Security Events Connector. After 24 hours we use a KQL query to see all 68,000+ failed login attempts to our Virtual Machine from around the world.</h3>



<p align="center">
<img width="85%" height="85%" alt="Screenshot (48)" src="https://github.com/user-attachments/assets/db7b9874-8ec0-4671-86b6-c856b56f1a74" />




<h3>Step 11: We then add a watchlist in Sentinel  and upload a file that maps IP ranges to physical locations around the world to visualize where our attacks came from.</h3>



<p align="center">
<img width="85%" height="85%" alt="Screenshot (50)" src="https://github.com/user-attachments/assets/fba62da9-dbe8-4413-9479-ed3c41d7ec34" />


<img width="85%" height="85%" alt="Screenshot (52)" src="https://github.com/user-attachments/assets/ee21a478-60e2-442b-86b2-7fa6b3fcf4fc" />




<h3>Step 12: We run a query to view our logs in a more granular way, down to the longitude, latitude and city names that are associated with the specific IP address. </h3>



<p align="center">
<img width="85%" height="85%" alt="Screenshot (53)" src="https://github.com/user-attachments/assets/be14cc2b-2bd5-4ac8-bc34-bd04edc71a9a" />




<h3>Step 13: Next we create a workbook in Sentinel and replace the auto-generated query with a JSON file.</h3>



<p align="center">
<img width="85%" height="85%" alt="Screenshot (54)" src="https://github.com/user-attachments/assets/06e40105-94fc-42bb-ae1b-4d5346a3510d" />


<img width="85%" height="85%" alt="Screenshot (58)" src="https://github.com/user-attachments/assets/6716a031-edd2-4be2-aa38-97f30a48b4b9" />




<h3>Step 14: Thanks to the JSON file we uploaded, the data that we collected is generated into an Attack Map to visualize where our attacks have come from.</h3>



<p align="center">
<img width="85%" height="85%" alt="Screenshot (59)" src="https://github.com/user-attachments/assets/9ca4229c-c95e-494b-bdf8-9a670d2545c2" />



