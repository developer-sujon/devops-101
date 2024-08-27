# Uploads Folder Transfer Documentation

This guide provides instructions for transferring the `uploads` folder between your local machine and a remote server using the `scp` command.

## Prerequisites

- Access to a terminal on your local machine.
- SSH credentials (username and password) for the remote server.

## Source Directory

- **Local Path:** `/home/sujon/Downloads/uploads`

## Destination Directory

- **Remote Server Path:** `/home/sroot/fms/src/uploads`
- **SSH Login:** `sroot@20.191.155.231`
- **Password:** `[HIDDEN]`

## Steps

### A. Transfer from Local to VPS

1. **Open the Terminal:**

   - Open a terminal on your local machine.

2. **Run the `scp` Command:**

   - Use the following command to copy the `uploads` folder from your local machine to the remote server:

     ```bash
     scp -r ~/Downloads/uploads/ sroot@20.191.155.231:/home/sroot/fms/src/uploads/
     ```

3. **Enter the Password:**

   - When prompted, enter the password for the `sroot` user.

4. **Verify the Transfer:**

   - Connect to the remote server using SSH:

     ```bash
     ssh sroot@20.191.155.231
     ```

   - List the contents of the destination directory to verify the transfer:

     ```bash
     ls /home/sroot/fms/src/uploads/
     ```

### B. Transfer from VPS to Local

1. **Open the Terminal:**

   - Open a terminal on your local machine.

2. **Run the `scp` Command:**

   - Use the following command to copy the `uploads` folder from the remote server to your local machine:

     ```bash
     scp -r sroot@20.191.155.231:/home/sroot/fms/src/uploads/ ~/Downloads/uploads/
     ```

3. **Enter the Password:**

   - When prompted, enter the password for the `sroot` user.

4. **Verify the Transfer:**

   - On your local machine, navigate to the destination directory and list the contents to verify the transfer:

     ```bash
     ls ~/Downloads/uploads/
     ```

## Notes

- Ensure that the destination directory exists on both the local and remote servers.
- If the SSH port is not the default (22), use the `-P` option with `scp` to specify the port number:

  ```bash
  scp -P [PORT] -r ~/Downloads/uploads/ sroot@20.191.155.231:/home/sroot/fms/src/uploads/
  ```
