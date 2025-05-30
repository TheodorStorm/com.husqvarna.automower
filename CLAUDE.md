# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Homey smart-home app for controlling Husqvarna Automowers equipped with Automower Connect. The app integrates with the Husqvarna API to provide flow-based automation controls for robotic lawnmowers.

## Development Commands

No build, lint, or test scripts are defined in package.json. This is a standard Homey app that runs directly on the Homey platform.

## Architecture

### Core Components

- **app.js**: Main application entry point, extends Homey.App class
- **drivers/mower/driver.js**: Mower driver that handles device discovery and flow card registration
- **drivers/mower/device.js**: Individual mower device implementation with capability management and polling
- **lib/automowerapiutil.js**: Husqvarna API client handling authentication and mower API calls
- **lib/positionutil.js**: Geolocation utilities using Turf.js for polygon-based position checks

### Authentication System

The app supports two authentication methods:
1. Legacy: username/password + appkey (grant_type: 'password')
2. Current: appkey/appsecret (grant_type: 'client_credentials')

The system prefers client_credentials when both sets of credentials are available. Authentication tokens are cached to minimize API calls.

### Device Capabilities & Flow Cards

**Capabilities tracked:**
- Activity, State, Mode, Error code, Battery level
- Next start time, Inactive reason, Last position
- Alarm for error conditions

**Flow Actions:** pause, park (various modes), resume, start, poll, confirm_current_error
**Flow Conditions:** activity/state checks, position comparisons, polygon containment
**Flow Triggers:** capability change events

### Polling System

Devices poll the Husqvarna API at configurable intervals (default: 10 minutes). Polling can be enabled/disabled per device and manually triggered via flow actions. Rate limits: 10,000 API calls per month per account.

### Position Features

Advanced geolocation features using Turf.js:
- Point-in-polygon checking for area-based automations
- Supports GeoJSON polygon definitions (Polygon, MultiPolygon, FeatureCollection)
- Latitude/longitude comparison conditions

### Error Handling

- Error codes are mapped through errorcodes.js
- Capability management includes automatic addition of new capabilities for backward compatibility
- API errors are handled with device warnings and logging