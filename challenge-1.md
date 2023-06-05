# Data Packet Corruption Detection
* In a Wireless communciation device, multiple data packets are transferred over the air. 
* It is possible that data might get corrupted or lost before it reaches the destination.
* Create a method to identify if a data packet is corrupted
* Assume below format for the data packet

```
#include <stdio.h>
#include <stdint.h>

#define MAX_PACKET_DATA_LENGTH (50)

typedef struct data_packet_t {
    uint8_t id;
    uint8_t data_length;
    uint8_t data[MAX_PACKET_DATA_LENGTH];
    uint16_t crc;
} data_packet_t;

uint16_t calculate_crc(const data_packet_t* packet) {
    // CRC algorithm implementation
    uint16_t crc = 0xFFFF; // Initial value

    // Update CRC with packet ID
    crc ^= packet->id;

    // Update CRC with packet data length
    crc ^= packet->data_length;

    // Update CRC with packet data
    for (int i = 0; i < packet->data_length; i++) {
        crc ^= packet->data[i];
        for (int j = 0; j < 8; j++) {
            if (crc & 0x0001)
                crc = (crc >> 1) ^ 0xA001; // XOR with polynomial
            else
                crc >>= 1;
        }
    }

    return crc;
}

int is_packet_corrupted(const data_packet_t* packet) {
    // Calculate CRC for the received packet
    uint16_t calculated_crc = calculate_crc(packet);

    // Compare the calculated CRC with the packet's CRC
    if (calculated_crc == packet->crc)
        return 0; // Packet is not corrupted
    else
        return 1; // Packet is corrupted
}

int main() {
    data_packet_t packet;

    // Simulating a corrupted packet
    packet.id = 1;
    packet.data_length = 4;
    packet.data[0] = 0x12;
    packet.data[1] = 0x34;
    packet.data[2] = 0x56;
    packet.data[3] = 0x78;
    packet.crc = calculate_crc(&packet) + 1; // Introducing corruption

    // Check if the packet is corrupted
    if (is_packet_corrupted(&packet))
        printf("Packet is corrupted.\n");
    else
        printf("Packet is not corrupted.\n");

    return 0;
}
