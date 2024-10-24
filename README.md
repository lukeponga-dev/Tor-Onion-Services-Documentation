# Visual Guide to Tor Onion Services

## Step 1: Service Advertises Its Presence

The Onion Service selects several Tor relays to act as **Introduction Points** and establishes secure circuits to them.

        [Onion Service] 
       /       |       \ 
      /        |         \ 
    [Intro1] [Intro2] [Intro3]

- **Secure Circuits** are established (the lines represent Tor circuits, not direct connections).
- **Introduction Points** know the service's public key but not its IP address, preserving anonymity.

---

## Step 2: Creating the Service Descriptor

The service compiles an **Onion Service Descriptor** containing its public key and information about the introduction points.

      [Onion Service]--[Publishes Descriptor]--> [Distributed Hash Table (DHT)] 


- The descriptor is **signed** with the service's private key for authenticity.
- Uploaded to the **DHT**, a decentralized network database accessible by clients.

---

## Step 3: Client Seeks to Connect

The client wants to access the onion service and needs to find its descriptor.

    [Client] --[Fetches Descriptor]--> [DHT]

- The client retrieves the descriptor using the onion address (e.g., `XYZ.onion`).
- Now aware of introduction points and the service's public key.

---

## Step 4: Client Sets Up a Rendezvous Point

The client establishes a rendezvous point to facilitate a connection without revealing identities.

    [Client] --[Establishes Circuit]--> [Rendezvous Point]

- A **one-time secret** is generated to secure the connection.
- The rendezvous point is a Tor relay chosen at random.

---

## Step 5: Client Introduces Itself to the Service

The client sends an introduction message via an introduction point.

    [Client] 
        | 
    [Intro Message (Encrypted with Service's Public Key)] 
        | 
    [Introduction Point] --[Forwards]--> [Onion Service]

  
- The message includes the rendezvous point address and the one-time secret.
- Sent over a Tor circuit to maintain the client's anonymity.

---

## Step 6: Service Connects to the Rendezvous Point

The onion service receives the introduction message and responds.





