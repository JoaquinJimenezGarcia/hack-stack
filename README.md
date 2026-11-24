# hack-stack

Kali Linux and a local practice site, run with Docker and stopped when you are not using them. Your everyday OS stays as it is. Stopped containers use no CPU or RAM. The image stays on disk so the next start is quick.

## What you get

- **Kali** (`kalilinux/kali-rolling`) with a starter CLI tool set: nmap, sqlmap, nikto, gobuster, ffuf, dirb, hydra, john, wordlists, Impacket, Responder, and the usual network utilities.
- **OWASP Juice Shop** (optional), a deliberately vulnerable web app used in courses. It listens on `127.0.0.1` only, so other machines on your network cannot open it.

Wi-Fi monitor mode, USB adapters, and systemd services do not work in this container. GUI apps such as Burp Suite and the Wireshark window are not installed. Use the terminal tools, and use your normal browser for the practice site.

Use this only against the local lab or systems you are allowed to test.

## Prerequisites

Docker Engine and the Compose plugin:

https://docs.docker.com/engine/install/

If `docker info` says permission denied:

```bash
sudo usermod -aG docker "$USER"
```

Log out and back in after that.

## Daily use

From this folder:

```bash
./stack up          # start Kali
./stack shell       # root shell; type exit to leave
./stack status      # containers, CPU, and memory
./stack down        # stop and free CPU and RAM
```

The first `./stack up` downloads the Kali base image and installs the starter tools. That can take several minutes. The finished image is about 500 MB. Later starts take a few seconds.

Course files you want to keep belong in `./work`. Inside Kali that folder is `/work`.

```bash
./stack up lab
```

Then open http://127.0.0.1:3080 in your browser. From inside Kali the same site is http://juice-shop:3000. If port 3080 is already in use, change `JUICE_PORT` in `.env` and run `./stack up lab` again.

## More tools

The default set is the package list in `kali/packages.starter`. Add a package there and run `./stack rebuild` if you want it in every new container. `./stack up` reuses the image you already have.

For one-off use, install inside the running container. That lasts across `./stack down` and is removed by `./stack purge --yes` or a rebuild:

```bash
apt update && apt install -y tshark
```

Official Kali metapackages are larger. `kali-linux-headless` is the set Kali documents for a container and is several GB. To switch, edit `.env`:

```bash
TOOLSET=kali-linux-headless
```

Then run `./stack rebuild`. Other `kali-*` metapackage names work the same way. Names are listed at https://www.kali.org/docs/general-use/metapackages/.

`rockyou.txt` ships compressed:

```bash
gzip -d /usr/share/wordlists/rockyou.txt.gz
```

## Resources

`.env` sets ceilings, not reserved memory. Idle Kali is a sleeping process under those ceilings. `./stack down` releases the CPU and RAM. Disk used by the image remains until you remove it.

```bash
./stack purge --yes            # remove containers, keep images and ./work
./stack purge --yes --images   # also delete the images
```

Files created in `./work` from inside Kali are owned by root. If you cannot edit them on the host:

```bash
sudo chown -R "$USER:$USER" work
```

## Limits

The Kali container has `NET_RAW` and `NET_ADMIN` so scans and packet capture work on the lab network. It is not privileged and it does not use the host network. Do not publish Juice Shop on `0.0.0.0`.
