# Zoho Cliq SDK for Node.js

[![npm version](https://img.shields.io/npm/v/zoho-cliq-sdk.svg)](https://www.npmjs.com/package/zoho-cliq-sdk)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Node.js Version](https://img.shields.io/node/v/zoho-cliq-sdk.svg)](https://nodejs.org)
[![Documentation](https://img.shields.io/badge/docs-API-blue.svg)](https://github.com/yourusername/zoho-cliq-sdk)

A complete, feature-rich Node.js SDK for the Zoho Cliq API. Send messages, manage channels, handle files, create bots, and more with simple, intuitive methods.

## 🚀 Features

- **Complete API Coverage**: Messages, Channels, Users, Chats, Bots, Files, Events, Webhooks, and more
- **OAuth 2.0 Support**: Automatic token refresh and management
- **Webhook Integration**: Easy webhook message sending
- **Rich Messaging**: Cards, forms, buttons, and interactive elements
- **File Management**: Upload, download, and manage files
- **Event System**: Built-in event emitter for real-time updates
- **TypeScript Support**: Full type definitions included
- **Retry Logic**: Automatic retry with exponential backoff
- **Comprehensive Examples**: Ready-to-use code samples

## 📦 Installation

```bash
npm install zoho-cliq-sdk
```

or with yarn:

```bash
yarn add zoho-cliq-sdk
```

## 🔧 Quick Start

### Basic Setup

```javascript
const ZohoCliq = require('zoho-cliq-sdk');

// Initialize with OAuth credentials
const cliq = new ZohoCliq({
  clientId: 'YOUR_CLIENT_ID',
  clientSecret: 'YOUR_CLIENT_SECRET',
  refreshToken: 'YOUR_REFRESH_TOKEN',
  domain: '.com' // or .eu, .in, .au, etc.
});

// Send a simple message
await cliq.messages.send('general', 'Hello from Node.js!');
```

### Using Webhook (Simplest Method)

```javascript
const ZohoCliq = require('zoho-cliq-sdk');

const cliq = new ZohoCliq({
  webhookToken: 'YOUR_WEBHOOK_TOKEN'
});

// Send via webhook
await cliq.webhooks.send('Hello World!');
```

## 📚 API Documentation

### Messages

```javascript
// Send simple message
await cliq.messages.send('channel-name', 'Hello!');

// Send formatted message with card
await cliq.messages.sendCard('channel-name', {
  title: 'Important Update',
  text: '**New feature** released!',
  theme: 'modern-inline',
  thumbnail: 'https://example.com/image.png',
  buttons: [{
    label: 'View Details',
    action: {
      type: 'open.url',
      data: { web: 'https://example.com' }
    }
  }]
});

// Send interactive form
await cliq.messages.sendForm('channel-name', {
  title: 'Quick Survey',
  fields: [
    {
      type: 'text',
      label: 'Name',
      name: 'name',
      mandatory: true
    },
    {
      type: 'radio',
      label: 'Rating',
      name: 'rating',
      options: [
        { label: 'Excellent', value: '5' },
        { label: 'Good', value: '4' },
        { label: 'Average', value: '3' }
      ]
    }
  ]
});

// Edit message
await cliq.messages.edit('message-id', 'Updated text');

// Delete message
await cliq.messages.delete('message-id');

// React to message
await cliq.messages.react('message-id', '👍');

// Pin message
await cliq.messages.pin('chat-id', 'message-id', {
  notify: true,
  expiryTime: -1 // Permanent
});

// Schedule message
await cliq.messages.schedule('channel-name', 'Reminder!', Date.now() + 3600000);

// Broadcast to multiple channels
await cliq.messages.broadcast(['general', 'announcements'], 'Company update!');

// Search messages
const results = await cliq.messages.search('keyword', {
  limit: 10,
  from_date: '2024-01-01'
});
```

### Channels

```javascript
// Create channel
const channel = await cliq.channels.create({
  name: 'project-alpha',
  description: 'Project Alpha Team',
  level: 'organization', // or 'team', 'private'
  inviteOnly: false,
  userIds: ['user1', 'user2']
});

// Get channel details
const info = await cliq.channels.get('channel-id');

// List all channels
const channels = await cliq.channels.list({
  limit: 50,
  joined: true
});

// Update channel
await cliq.channels.update('channel-id', {
  description: 'Updated description'
});

// Archive/Unarchive
await cliq.channels.archive('channel-id');
await cliq.channels.unarchive('channel-id');

// Manage members
await cliq.channels.addMembers('channel-id', ['user3', 'user4']);
await cliq.channels.removeMembers('channel-id', ['user5']);

// Join/Leave channel
await cliq.channels.join('channel-id');
await cliq.channels.leave('channel-id');

// Mute/Unmute
await cliq.channels.mute('channel-id', 3600000); // 1 hour
await cliq.channels.unmute('channel-id');
```

### Users

```javascript
// Get user details
const user = await cliq.users.get('user-id');

// Get current user
const me = await cliq.users.me();

// List all users
const users = await cliq.users.list({
  limit: 100,
  status: 'active'
});

// Update profile
await cliq.users.updateProfile('user-id', {
  display_name: 'John Doe',
  mobile: '+1234567890'
});

// Set status
await cliq.users.setStatus({
  code: 'busy', // available, busy, invisible
  message: 'In a meeting'
});

// Search users
const results = await cliq.users.search('john', {
  limit: 10
});
```

### Files

```javascript
// Upload file
const file = await cliq.files.upload('./document.pdf', 'channel-id');

// Download file
await cliq.files.download('file-id', './downloads/document.pdf');

// Get file info
const info = await cliq.files.getInfo('file-id');

// Delete file
await cliq.files.delete('file-id');
```

### Bots

```javascript
// Send bot message
await cliq.bots.sendMessage('bot-id', 'channel-name', {
  text: 'Bot notification',
  suggestions: ['Yes', 'No', 'Maybe'],
  card: {
    title: 'Bot Card',
    theme: 'modern-inline'
  }
});

// Handle slash command
await cliq.bots.handleCommand({
  command: '/weather',
  params: 'New York',
  user_id: 'user-id'
});

// Create bot
const bot = await cliq.bots.create({
  name: 'Helper Bot',
  description: 'Automated helper',
  webhookUrl: 'https://your-server.com/bot-webhook'
});
```

### Events

```javascript
// Create event
const event = await cliq.events.create({
  title: 'Team Meeting',
  description: 'Weekly sync',
  startTime: Date.now() + 86400000,
  endTime: Date.now() + 90000000,
  channelId: 'channel-id',
  reminder: '15m'
});

// Update event
await cliq.events.update('event-id', {
  title: 'Updated Meeting Title'
});

// List events
const events = await cliq.events.list({
  from_date: '2024-01-01',
  to_date: '2024-12-31'
});
```

### Threads

```javascript
// Reply to thread
await cliq.threads.reply('parent-message-id', 'Thread reply');

// Get thread messages
const messages = await cliq.threads.getMessages('parent-message-id');
```

### Webhooks

```javascript
// Send simple webhook message
await cliq.webhooks.send('Hello via webhook!');

// Send rich webhook message
await cliq.webhooks.sendRich('webhook-token', {
  text: 'Rich message',
  card: {
    title: 'Webhook Card',
    theme: 'modern-inline'
  },
  buttons: [{
    label: 'Click me',
    action: {
      type: 'open.url',
      data: { web: 'https://example.com' }
    }
  }]
});
```

## 🎯 Complete Examples

### Example 1: Send Daily Report

```javascript
const ZohoCliq = require('zoho-cliq-sdk');

async function sendDailyReport() {
  const cliq = new ZohoCliq({
    clientId: process.env.CLIENT_ID,
    clientSecret: process.env.CLIENT_SECRET,
    refreshToken: process.env.REFRESH_TOKEN
  });

  const reportData = {
    sales: 150,
    newUsers: 45,
    revenue: '$12,500'
  };

  await cliq.messages.sendCard('reports', {
    title: '📊 Daily Report',
    text: `**Date**: ${new Date().toLocaleDateString()}`,
    theme: 'modern-inline',
    slides: [{
      type: 'table',
      title: 'Metrics',
      data: {
        headers: ['Metric', 'Value'],
        rows: [
          ['Sales', reportData.sales],
          ['New Users', reportData.newUsers],
          ['Revenue', reportData.revenue]
        ]
      }
    }],
    buttons: [{
      label: 'View Dashboard',
      action: {
        type: 'open.url',
        data: { web: 'https://dashboard.example.com' }
      }
    }]
  });
}

sendDailyReport();
```

### Example 2: Interactive Bot

```javascript
const express = require('express');
const ZohoCliq = require('zoho-cliq-sdk');

const app = express();
app.use(express.json());

const cliq = new ZohoCliq({
  webhookToken: process.env.WEBHOOK_TOKEN
});

// Handle bot commands
app.post('/bot-webhook', async (req, res) => {
  const { command, params, user, channel } = req.body;

  switch(command) {
    case '/help':
      await cliq.webhooks.send({
        text: 'Available commands:\n/help - Show this message\n/weather [city] - Get weather\n/task [description] - Create task'
      });
      break;
      
    case '/weather':
      const weather = await getWeather(params);
      await cliq.messages.sendCard(channel, {
        title: `Weather in ${params}`,
        text: weather.description,
        theme: 'modern-inline'
      });
      break;
      
    case '/task':
      await cliq.messages.sendForm(channel, {
        title: 'Create Task',
        fields: [
          {
            type: 'text',
            label: 'Task Description',
            name: 'description',
            value: params,
            mandatory: true
          },
          {
            type: 'select',
            label: 'Priority',
            name: 'priority',
            options: [
              { label: 'High', value: 'high' },
              { label: 'Medium', value: 'medium' },
              { label: 'Low', value: 'low' }
            ]
          }
        ]
      });
      break;
  }
  
  res.json({ success: true });
});

app.listen(3000, () => {
  console.log('Bot server running on port 3000');
});
```

### Example 3: File Upload with Notification

```javascript
const ZohoCliq = require('zoho-cliq-sdk');
const fs = require('fs');

async function uploadAndNotify() {
  const cliq = new ZohoCliq({
    clientId: process.env.CLIENT_ID,
    clientSecret: process.env.CLIENT_SECRET,
    refreshToken: process.env.REFRESH_TOKEN
  });

  // Upload file
  const file = await cliq.files.upload('./report.pdf', 'documents');
  
  // Send notification with file link
  await cliq.messages.sendCard('general', {
    title: '📎 New File Uploaded',
    text: `**File**: report.pdf\n**Size**: ${file.size}\n**Uploaded by**: ${file.uploader}`,
    theme: 'modern-inline',
    buttons: [{
      label: 'Download File',
      action: {
        type: 'open.url',
        data: { web: file.download_url }
      }
    }]
  });
}

uploadAndNotify();
```

## 🔐 Environment Variables

Create a `.env` file:

```bash
# OAuth Configuration
ZOHO_CLIENT_ID=your_client_id
ZOHO_CLIENT_SECRET=your_client_secret
ZOHO_REFRESH_TOKEN=your_refresh_token

# Webhook Configuration
ZOHO_WEBHOOK_TOKEN=your_webhook_token

# Optional
ZOHO_DOMAIN=.com
DEBUG=true
```

## 🎪 Event Handling

The SDK emits events for various operations:

```javascript
const cliq = new ZohoCliq(config);

// Token refreshed
cliq.on('token:refreshed', (data) => {
  console.log('New access token:', data.accessToken);
});

// Error occurred
cliq.on('error', (error) => {
  console.error('Error:', error);
});

// Message sent
cliq.on('message:sent', (data) => {
  console.log('Message sent to:', data.channel);
});
```

## 🧪 Testing

```bash
# Run all tests
npm test

# Run tests with coverage
npm run test:coverage

# Run tests in watch mode
npm run test:watch
```

## 📝 TypeScript Support

The SDK includes TypeScript definitions:

```typescript
import ZohoCliq, { MessageOptions, ChannelData } from 'zoho-cliq-sdk';

const cliq = new ZohoCliq({
  clientId: 'your_client_id',
  clientSecret: 'your_client_secret',
  refreshToken: 'your_refresh_token'
});

const message: MessageOptions = {
  text: 'Hello TypeScript!',
  card: {
    title: 'TypeScript Message',
    theme: 'modern-inline'
  }
};

await cliq.messages.send('channel', message.text, message);
```

## 🚀 Advanced Configuration

```javascript
const cliq = new ZohoCliq({
  // OAuth credentials
  clientId: 'your_client_id',
  clientSecret: 'your_client_secret',
  refreshToken: 'your_refresh_token',
  
  // Optional configurations
  domain: '.com',              // Zoho domain
  autoRefreshToken: true,      // Auto-refresh OAuth token
  retryAttempts: 3,           // Number of retry attempts
  timeout: 30000,             // Request timeout in ms
  debug: true                 // Enable debug logging
});
```

## 📊 Rate Limits

The SDK automatically handles rate limiting:

| API | Rate Limit | Daily Limit |
|-----|------------|-------------|
| Messages | 60/min | 10,000 |
| Channels | 30/min | 5,000 |
| Files | 30/min | 1,000 |
| Users | 100/min | 50,000 |

## 🤝 Contributing

Contributions are welcome! Please read our [Contributing Guide](CONTRIBUTING.md) for details.

```bash
# Fork and clone the repository
git clone https://github.com/yourusername/zoho-cliq-sdk.git

# Install dependencies
npm install

# Run tests
npm test

# Submit a pull request
```

## 📄 License

MIT License - see [LICENSE](LICENSE) file for details.

## 🔗 Resources

- [Zoho Cliq API Documentation](https://www.zoho.com/cliq/help/restapi/v2/)
- [OAuth 2.0 Setup Guide](https://www.zoho.com/cliq/help/restapi/v2/#oauth)
- [Webhook Documentation](https://www.zoho.com/cliq/help/platform/webhooks.html)
- [npm Package](https://www.npmjs.com/package/zoho-cliq-sdk)

## 💬 Support

- **Issues**: [GitHub Issues](https://github.com/yourusername/zoho-cliq-sdk/issues)
- **Discussions**: [GitHub Discussions](https://github.com/yourusername/zoho-cliq-sdk/discussions)
- **Email**: support@example.com

---

Made with ❤️ by the Node.js community