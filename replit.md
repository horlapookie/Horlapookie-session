# Horlapookie Session - WhatsApp Bot Session Generator

## Overview

Horlapookie Session is a lightweight web application that generates authentication sessions for WhatsApp bots built on the Baileys library. The application provides two authentication methods: QR code scanning and pairing code generation. Once authenticated, it generates encrypted session credentials that can be used to connect WhatsApp bots without requiring repeated authentication.

The system acts as a session generator service, handling the initial WhatsApp authentication flow and providing users with portable, encrypted session data that can be deployed across different bot implementations.

## User Preferences

Preferred communication style: Simple, everyday language.

## System Architecture

### Frontend Architecture
- **Static HTML Pages**: The application uses vanilla HTML/CSS/JavaScript without any frontend framework
- **Dual Interface Design**: Two separate interfaces are provided:
  - `main.html`: Landing page with routing to authentication options
  - `pair.html`: Pairing code authentication interface
- **Client-Side Rendering**: All UI rendering happens directly in the browser with no build process
- **Responsive Design**: CSS uses modern flexbox/grid layouts with glassmorphism effects and mobile-first approach

### Backend Architecture
- **Express.js Web Server**: Lightweight Node.js server handling HTTP requests
- **Route-Based Handlers**: Separate router modules for different authentication methods:
  - `/qr` route: Handles QR code generation and scanning
  - `/code` route: Handles pairing code generation
  - `/pair` route: Serves the pairing interface
- **Stateless Design**: Each authentication session creates temporary files that are cleaned up after credential generation
- **Event-Driven Flow**: Uses Baileys event emitters for connection state management

### Authentication Flow
- **Baileys Integration**: Core authentication handled by @whiskeysockets/baileys library
- **Multi-File Auth State**: Credentials stored using Baileys' `useMultiFileAuthState` mechanism
- **Temporary Session Storage**: Each authentication attempt creates a unique temporary directory (`./temp/[id]`)
- **Two Authentication Methods**:
  1. QR Code: Generates scannable QR for WhatsApp mobile app
  2. Pairing Code: Generates 8-digit code for WhatsApp's "Link a Device" feature
- **Session Cleanup**: Temporary files automatically removed after successful credential generation

### Data Storage
- **File-Based Sessions**: Authentication state stored in temporary filesystem directories
- **No Database Required**: The application doesn't persist user data beyond the authentication flow
- **Session Format**: Credentials encoded in Base64 format with `horlapookie~` prefix
- **Credential Structure**: Baileys-compatible JSON format containing auth keys and device information

### Session Generation Process
1. User initiates authentication (QR or pairing code)
2. Unique session ID generated using random string utility
3. Temporary directory created at `./temp/[sessionId]`
4. Baileys connection established with specified browser configuration
5. Authentication credentials captured via event listeners
6. Credentials uploaded to Pastebin for easy retrieval
7. Session data returned to user with cleanup of temporary files

## External Dependencies

### Core Libraries
- **@whiskeysockets/baileys** (v6.7.20): WhatsApp Web API implementation
  - Handles WebSocket connections to WhatsApp servers
  - Manages encryption/decryption of messages
  - Provides multi-device authentication support
  - Browser emulation configured as Ubuntu Chrome or macOS Chrome

### Web Framework
- **Express.js** (v4.18.1): HTTP server and routing
- **body-parser** (v1.20.1): Request body parsing middleware

### Utilities
- **qrcode** (v1.5.3): QR code generation for visual authentication
- **awesome-phonenumber** (v2.64.0): Phone number validation and formatting
- **phone** (v3.1.30): Additional phone number utilities
- **pino** (v8.1.0): High-performance logging (configured to 'silent'/'fatal' levels)
- **jimp** (v1.6.0): Image processing library

### External Services
- **Pastebin API**: Credential storage and sharing
  - API Key: Hard-coded in application
  - Used to upload generated session credentials
  - Provides shareable links for credential retrieval

### Deployment Considerations
- **Node.js Runtime**: Requires Node.js >=20.0.0
- **npm Version**: Requires npm >=9.7.2
- **Port Configuration**: Defaults to PORT environment variable or 5000
- **Heroku Buildpacks**: Configured for Heroku deployment with FFmpeg and WebP support

### Security Notes
- Session credentials are highly sensitive and provide full WhatsApp account access
- Temporary files contain unencrypted authentication state during generation
- Pastebin uploads create publicly accessible credential links
- No rate limiting or authentication on session generation endpoints