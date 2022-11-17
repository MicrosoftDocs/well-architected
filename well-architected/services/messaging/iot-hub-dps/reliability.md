---
title: IoT Hub Device Provisioning Service and reliability
description: Focuses on the IoT Hub Device Provisioning Service (DPS) used in the Messaging solution to provide design considerations related to Reliability.
author: v-stacywray
ms.author: martinekuan
ms.date: 01/19/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - iot-hub-device-provisioning-service
categories:
  - messaging
  - management-and-governance
---

# IoT Hub Device Provisioning Service and reliability

The [IoT Hub Device Provisioning Service (DPS)](/azure/iot-dps/) is a helper service for IoT Hub. DPS enables zero-touch, just-in-time provisioning to the right IoT Hub without requiring human intervention. IoT Hub DPS allows customers to provision millions of devices in a secure and scalable manner.

For more information, reference [What is Azure IoT Hub Device Provisioning Service?](/azure/iot-dps/about-iot-dps)

To understand how IoT Hub DPS can increase workload reliability, reference [IoT Hub DPS supports Availability Zones](/azure/iot-dps/about-iot-dps#availability).

## Design considerations

For more information about the Service Level Agreement for Azure IoT Hub DPS, reference [SLA for Azure IoT Hub](https://azure.microsoft.com/support/legal/sla/iot-hub/v1_2/).

## Next step

> [!div class="nextstepaction"]
> [IoT Hub Device Provisioning Service and operational excellence](operational-excellence.md)