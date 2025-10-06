**Findings**

**1. Nmap Scan**

An Nmap scan identified the following open port and service:

- **Port 5000**: A web service was running.

<img width="717" height="550" alt="image" src="https://github.com/user-attachments/assets/eed07366-ce8f-44c8-818c-e8a96123cdc8" />
<img width="715" height="553" alt="image" src="https://github.com/user-attachments/assets/cdb8dede-39fb-4fdf-a8ee-d43514df1f56" />



**2. Initial Exploitation**

The web service on port 5000 allowed uploading .cif files. Upon further
research, the service was found to be vulnerable to **CVE-2024-23346**,
enabling **Remote Code Execution (RCE)**.

- **Attack**: Uploaded a malicious .cif file to achieve a reverse shell.

<img width="495" height="380" alt="image" src="https://github.com/user-attachments/assets/7a40c698-b690-4bbe-948a-36acd006dd03" />



<img width="873" height="467" alt="image" src="https://github.com/user-attachments/assets/ddbf3a8f-1919-470b-abc9-0a4f1b8631d0" />


<https://github.com/materialsproject/pymatgen/security/advisories/GHSA-vgv8-5cpj-qj2f>

**3. Gaining SSH Access**

After obtaining a shell, a search of the file system uncovered a
database file named database.b containing credentials for the SSH user
rosa.

- **Credentials Extracted**: Successfully used the credentials to log in
  via SSH:

> ssh <rosa@10.10.11.38>
>
 <img width="760" height="587" alt="image" src="https://github.com/user-attachments/assets/2539b4c4-907a-40d2-a063-27afdad55df2" />
 <img width="757" height="585" alt="image" src="https://github.com/user-attachments/assets/70cd747c-0994-4434-8b00-b972277a3354" />



**4. Locating the User Flag**

- Upon gaining SSH access, the **user.txt** flag was located and
  captured.

**5. Local Enumeration**

While enumerating the system, a locally hosted service was identified on
port **8080**.

- The service was not accessible remotely as it was bound to 127.0.0.1.

- **Command Used**:

> netstat --tuln
>
<img width="805" height="622" alt="image" src="https://github.com/user-attachments/assets/f26b5cb3-15ed-4a65-b3f1-221fc2d2e2a2" />

**6. Port Forwarding**

To access the service on port 8080 remotely, SSH port forwarding was
utilized:

ssh -L 8080:127.0.0.1:8080 rosa@10.10.11.38

This exposed the local service to the attacker\'s machine for further
analysis.

**7. Vulnerability Discovery**

To analyze the service, the following command was used to retrieve the
HTTP response headers:

curl -I <http://127.0.0.1:8080>

<img width="848" height="655" alt="image" src="https://github.com/user-attachments/assets/bc2efd27-7cad-4627-b72d-049a9d383054" />

The headers revealed the use of **aiohttp**, a Python web framework,
which was an outdated version vulnerable to **CVE-2024-23334**. This
vulnerability allows for remote exploitation under specific conditions.

**1. SSH Tunneling Setup**

- After gaining initial foothold with user rosa through a database
  extraction and SSH credentials:

  - **SSH command used**:

> ssh -L 5001:127.0.0.1:8080 rosa@10.10.11.38

- This forwarded the internal port 8080 on the target to 5001 on the
  attacker\'s local machine for easier interaction.

**2. Accessing the Hidden Service**

- **Validation**: Tested the forwarded port locally with:

> curl -I http://localhost:5001

- **Response**:

> HTTP/1.1 200 OK
>
> Content-Type: text/html; charset=utf-8
>
> Content-Length: 5971
>
> Date: Sun, 17 Nov 2024 20:27:00 GMT
>
> Server: Python/3.9 aiohttp/3.9.1

**3. Vulnerability Analysis**

- The response header revealed the service was powered by aiohttp/3.9.1,
  which is known to have vulnerabilities:

  - **Identified CVE**: CVE-2023-30589

  - **Description**: aiohttp versions prior to 3.9.2 are vulnerable to
    directory traversal attacks, allowing unauthorized access to files
    outside the intended directory.

**4. Exploitation**

- Leveraged the directory traversal vulnerability to access the root.txt
  file:

> curl \--path-as-is
> \"http://localhost:5001/assets/../../../../../root/root.txt\"
