# OpenK API Documentation

## Overview

This document provides comprehensive API documentation for the OpenK (Open Kingdom) backend service. All APIs use centralized error handling and return standardized JSON responses.

**Base URL**: `https://your-api-gateway-url.com` or `http://localhost:3000` for local development

## Authentication

Most endpoints require authentication via JWT token in the Authorization header:
```
Authorization: Bearer <your-jwt-token>
```

## Error Handling

All APIs use centralized error handling with standardized error responses:

```json
{
  "message": "Error description",
  "code": "ERROR_CODE",
  "details": {} // Optional, for validation errors
}
```

**HTTP Status Codes:**
- `200` - Success
- `201` - Created
- `400` - Bad Request
- `401` - Unauthorized
- `403` - Forbidden
- `404` - Not Found
- `422` - Validation Error
- `500` - Internal Server Error

---

## Health Check

### GET /health

Check service health status.

**Request:**
```http
GET /health
```

**Response:**
```json
{
  "status": "healthy",
  "timestamp": "2024-01-01T00:00:00.000Z",
  "service": "open-kingdom-backend",
  "version": "1.0.0"
}
```

---

## Authentication APIs

### POST /auth/login

Authenticate user and get access tokens.

**Request:**
```http
POST /auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "password123"
}
```

**Response:**
```json
{
  "user": {
    "id": "1",
    "email": "user@example.com",
    "phone": "+12025550123",
    "status": "active",
    "countryCode": "US",
    "language": "en",
    "refCode": "ABC12345",
    "createdAt": "2024-01-01T00:00:00.000Z",
    "profile": {
      "firstName": "John",
      "lastName": "Doe"
    },
    "identities": []
  },
  "tokens": {
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expiresIn": 3600
  }
}
```

### POST /auth/register

Register a new user account.

**Request:**
```http
POST /auth/register
Content-Type: application/json

{
  "email": "new@example.com",
  "password": "Password123",
  "phone": "+12025550123",
  "refCode": "ABC12345",
  "countryCode": "US",
  "language": "en"
}
```

**Response:**
```json
{
  "user": {
    "id": "2",
    "email": "new@example.com",
    "phone": "+12025550123",
    "status": "active",
    "countryCode": "US",
    "language": "en",
    "refCode": "XYZ67890",
    "createdAt": "2024-01-01T00:00:00.000Z",
    "profile": null,
    "identities": []
  },
  "tokens": {
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expiresIn": 3600
  }
}
```

### POST /auth/logout

Logout user and invalidate refresh token.

**Request:**
```http
POST /auth/logout
Authorization: Bearer <token>
Content-Type: application/json

{
  "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

**Response:**
```json
{
  "message": "Logged out successfully"
}
```

### POST /auth/refresh

Refresh access token using refresh token.

**Request:**
```http
POST /auth/refresh
Content-Type: application/json

{
  "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

**Response:**
```json
{
  "tokens": {
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expiresIn": 3600
  }
}
```

---

## User APIs

### GET /users/me

Get current user profile information.

**Request:**
```http
GET /users/me
Authorization: Bearer <token>
```

**Response:**
```json
{
  "user": {
    "id": "1",
    "email": "user@example.com",
    "phone": "+12025550123",
    "status": "active",
    "countryCode": "US",
    "language": "en",
    "refCode": "ABC12345",
    "createdAt": "2024-01-01T00:00:00.000Z",
    "profile": {
      "firstName": "John",
      "lastName": "Doe",
      "dob": "1990-01-01T00:00:00.000Z",
      "address": {}
    },
    "identities": []
  }
}
```

### POST /users/change-password

Change user password.

**Request:**
```http
POST /users/change-password
Authorization: Bearer <token>
Content-Type: application/json

{
  "currentPassword": "oldPassword123",
  "newPassword": "newPassword123"
}
```

**Response:**
```json
{
  "message": "Password changed successfully"
}
```

### POST /users/update-profile

Update user profile information.

**Request:**
```http
POST /users/update-profile
Authorization: Bearer <token>
Content-Type: application/json

{
  "firstName": "John",
  "lastName": "Doe",
  "phone": "+12025550123",
  "countryCode": "US",
  "language": "en",
  "dob": "1990-01-01T00:00:00.000Z",
  "address": {
    "street": "123 Main St",
    "city": "New York",
    "state": "NY",
    "zipCode": "10001",
    "country": "US"
  }
}
```

**Response:**
```json
{
  "user": {
    "id": "1",
    "email": "user@example.com",
    "phone": "+12025550123",
    "status": "active",
    "countryCode": "US",
    "language": "en",
    "refCode": "ABC12345",
    "createdAt": "2024-01-01T00:00:00.000Z",
    "profile": {
      "firstName": "John",
      "lastName": "Doe",
      "dob": "1990-01-01T00:00:00.000Z",
      "address": {
        "street": "123 Main St",
        "city": "New York",
        "state": "NY",
        "zipCode": "10001",
        "country": "US"
      }
    },
    "identities": []
  }
}
```

---

## Wallet APIs

### GET /wallets/balance

Get user's wallet balances for all tokens.

**Request:**
```http
GET /wallets/balance
Authorization: Bearer <token>
```

**Response:**
```json
{
  "balances": [
    {
      "walletId": "1",
      "walletType": "main",
      "token": {
        "id": "1",
        "symbol": "OKT",
        "name": "Open Kingdom Token",
        "decimals": 6,
        "isNative": false,
        "status": "active"
      },
      "balance": "1000000000",
      "available": "1000000000",
      "locked": "0",
      "balanceFormatted": "1000.000000",
      "availableFormatted": "1000.000000",
      "lockedFormatted": "0.000000"
    }
  ],
  "summary": {
    "totalTokens": 1,
    "totalWallets": 1
  }
}
```

### GET /wallets/transactions

Get user's wallet transaction history.

**Request:**
```http
GET /wallets/transactions?page=1&limit=20
Authorization: Bearer <token>
```

**Query Parameters:**
- `page` (optional): Page number (default: 1)
- `limit` (optional): Items per page (default: 20, max: 100)

**Response:**
```json
{
  "transactions": [
    {
      "id": "1",
      "type": "transfer_in",
      "amount": "1000000000",
      "amountFormatted": "1000.000000",
      "token": {
        "id": "1",
        "symbol": "OKT",
        "name": "Open Kingdom Token",
        "decimals": 6
      },
      "fromUserId": "2",
      "toUserId": "1",
      "note": "Welcome bonus",
      "status": "completed",
      "createdAt": "2024-01-01T00:00:00.000Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 1,
    "totalPages": 1
  }
}
```

### POST /wallets/transfer

Transfer tokens to another user.

**Request:**
```http
POST /wallets/transfer
Authorization: Bearer <token>
Content-Type: application/json

{
  "toUserId": "2",
  "tokenId": "1",
  "amount": 1.5,
  "note": "Thanks for the help!"
}
```

**Response:**
```json
{
  "transaction": {
    "id": "1",
    "type": "transfer_out",
    "amount": "1500000",
    "amountFormatted": "1.500000",
    "token": {
      "id": "1",
      "symbol": "OKT",
      "name": "Open Kingdom Token",
      "decimals": 6
    },
    "fromUserId": "1",
    "toUserId": "2",
    "note": "Thanks for the help!",
    "status": "completed",
    "createdAt": "2024-01-01T00:00:00.000Z"
  }
}
```

---

## Campaign APIs

### GET /campaigns

List all campaigns with optional filtering.

**Request:**
```http
GET /campaigns?page=1&limit=20&status=active&type=airdrop
```

**Query Parameters:**
- `page` (optional): Page number (default: 1)
- `limit` (optional): Items per page (default: 20, max: 100)
- `status` (optional): Filter by status (`draft`, `active`, `paused`, `ended`)
- `type` (optional): Filter by type (`airdrop`, `referral`, `task`, `ad`)

**Response:**
```json
{
  "campaigns": [
    {
      "id": "1",
      "code": "welcome-airdrop",
      "name": "Welcome Airdrop Campaign",
      "type": "airdrop",
      "status": "active",
      "description": "Welcome to Open Kingdom! Complete daily check-ins to earn rewards.",
      "bannerUrl": "https://example.com/banner.jpg",
      "startAt": "2024-01-01T00:00:00.000Z",
      "endAt": "2024-01-31T23:59:59.000Z",
      "marketWhitelist": {},
      "rewardToken": {
        "id": "1",
        "symbol": "OKT",
        "name": "Open Kingdom Token",
        "decimals": 6
      },
      "tasks": [
        {
          "id": "1",
          "code": "daily_checkin",
          "name": "Daily Check-in",
          "type": "daily_checkin",
          "orderNo": 1,
          "isRepeatable": true,
          "cooldownSeconds": 86400
        }
      ],
      "participantCount": 150,
      "userParticipation": {
        "id": "1",
        "status": "active",
        "joinedAt": "2024-01-01T00:00:00.000Z"
      },
      "createdAt": "2024-01-01T00:00:00.000Z",
      "updatedAt": "2024-01-01T00:00:00.000Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 1,
    "totalPages": 1
  }
}
```

### GET /campaigns/:id

Get specific campaign details.

**Request:**
```http
GET /campaigns/1
```

**Response:**
```json
{
  "campaign": {
    "id": "1",
    "code": "welcome-airdrop",
    "name": "Welcome Airdrop Campaign",
    "type": "airdrop",
    "status": "active",
    "description": "Welcome to Open Kingdom! Complete daily check-ins to earn rewards.",
    "bannerUrl": "https://example.com/banner.jpg",
    "startAt": "2024-01-01T00:00:00.000Z",
    "endAt": "2024-01-31T23:59:59.000Z",
    "marketWhitelist": {},
    "rewardToken": {
      "id": "1",
      "symbol": "OKT",
      "name": "Open Kingdom Token",
      "decimals": 6
    },
    "tasks": [
      {
        "id": "1",
        "code": "daily_checkin",
        "name": "Daily Check-in",
        "type": "daily_checkin",
        "orderNo": 1,
        "isRepeatable": true,
        "cooldownSeconds": 86400
      }
    ],
    "participantCount": 150,
    "userParticipation": null,
    "createdAt": "2024-01-01T00:00:00.000Z",
    "updatedAt": "2024-01-01T00:00:00.000Z"
  }
}
```

### POST /campaigns/:id/join

Join a campaign.

**Request:**
```http
POST /campaigns/1/join
Authorization: Bearer <token>
```

**Response:**
```json
{
  "userCampaign": {
    "id": "1",
    "userId": "1",
    "campaignId": "1",
    "status": "active",
    "joinedAt": "2024-01-01T00:00:00.000Z"
  }
}
```

### GET /campaigns/:id/tasks

Get campaign tasks for the authenticated user.

**Request:**
```http
GET /campaigns/1/tasks
Authorization: Bearer <token>
```

**Response:**
```json
{
  "tasks": [
    {
      "id": "1",
      "code": "daily_checkin",
      "name": "Daily Check-in",
      "type": "daily_checkin",
      "orderNo": 1,
      "isRepeatable": true,
      "cooldownSeconds": 86400,
      "userProgress": {
        "completed": true,
        "completedAt": "2024-01-01T00:00:00.000Z",
        "nextAvailableAt": "2024-01-02T00:00:00.000Z",
        "completionCount": 1
      }
    }
  ]
}
```

### POST /campaigns/:id/tasks/:taskId/complete

Complete a campaign task.

**Request:**
```http
POST /campaigns/1/tasks/1/complete
Authorization: Bearer <token>
Content-Type: application/json

{
  "evidence": {
    "screenshot": "https://example.com/screenshot.jpg",
    "description": "Completed daily check-in"
  }
}
```

**Response:**
```json
{
  "taskCompletion": {
    "id": "1",
    "userId": "1",
    "campaignId": "1",
    "taskId": "1",
    "evidence": {
      "screenshot": "https://example.com/screenshot.jpg",
      "description": "Completed daily check-in"
    },
    "rewardAmount": "1000000",
    "rewardAmountFormatted": "1.000000",
    "completedAt": "2024-01-01T00:00:00.000Z"
  }
}
```

### GET /campaigns/rewards

Get user's campaign rewards.

**Request:**
```http
GET /campaigns/rewards?limit=20&offset=0
Authorization: Bearer <token>
```

**Query Parameters:**
- `limit` (optional): Items per page (default: 20)
- `offset` (optional): Number of items to skip (default: 0)

**Response:**
```json
{
  "rewards": [
    {
      "id": "1",
      "campaignId": "1",
      "taskId": "1",
      "amount": "1000000",
      "amountFormatted": "1.000000",
      "token": {
        "id": "1",
        "symbol": "OKT",
        "name": "Open Kingdom Token",
        "decimals": 6
      },
      "status": "completed",
      "completedAt": "2024-01-01T00:00:00.000Z"
    }
  ],
  "pagination": {
    "limit": 20,
    "offset": 0,
    "total": 1
  }
}
```

---

## Airdrop APIs

### POST /airdrops/checkin

Perform daily check-in for airdrop campaign.

**Request:**
```http
POST /airdrops/checkin
Authorization: Bearer <token>
Content-Type: application/json

{
  "campaignId": "1"
}
```

**Response:**
```json
{
  "checkin": {
    "id": "1",
    "userId": "1",
    "campaignId": "1",
    "rewardAmount": "1000000",
    "rewardAmountFormatted": "1.000000",
    "checkedInAt": "2024-01-01T00:00:00.000Z",
    "nextCheckinAt": "2024-01-02T00:00:00.000Z"
  }
}
```

### GET /airdrops/stats

Get airdrop statistics for the user.

**Request:**
```http
GET /airdrops/stats?limit=30
Authorization: Bearer <token>
```

**Query Parameters:**
- `limit` (optional): Number of days to include (default: 30)

**Response:**
```json
{
  "stats": {
    "totalCheckins": 15,
    "totalRewards": "15000000",
    "totalRewardsFormatted": "15.000000",
    "currentStreak": 5,
    "longestStreak": 10,
    "dailyStats": [
      {
        "date": "2024-01-01",
        "checkedIn": true,
        "rewardAmount": "1000000",
        "rewardAmountFormatted": "1.000000"
      }
    ]
  }
}
```

---

## KYC APIs

### POST /kyc/submit

Submit KYC documents for verification.

**Request:**
```http
POST /kyc/submit
Authorization: Bearer <token>
Content-Type: application/json

{
  "level": "basic",
  "documents": [
    {
      "docType": "id_front",
      "fileUrl": "https://example.com/id_front.jpg",
      "fileHash": "abc123def456"
    },
    {
      "docType": "selfie",
      "fileUrl": "https://example.com/selfie.jpg",
      "fileHash": "def456ghi789"
    }
  ]
}
```

**Response:**
```json
{
  "kycRequest": {
    "id": "1",
    "userId": "1",
    "level": "basic",
    "status": "pending",
    "submittedAt": "2024-01-01T00:00:00.000Z",
    "documents": [
      {
        "id": "1",
        "docType": "id_front",
        "fileUrl": "https://example.com/id_front.jpg",
        "fileHash": "abc123def456",
        "status": "pending",
        "createdAt": "2024-01-01T00:00:00.000Z"
      }
    ]
  }
}
```

### GET /kyc/status

Get current KYC status and history.

**Request:**
```http
GET /kyc/status
Authorization: Bearer <token>
```

**Response:**
```json
{
  "currentStatus": "pending",
  "currentLevel": "basic",
  "kycRequests": [
    {
      "id": "1",
      "level": "basic",
      "status": "pending",
      "submittedAt": "2024-01-01T00:00:00.000Z",
      "reviewedBy": null,
      "reviewedAt": null,
      "rejectReason": null,
      "documents": [
        {
          "id": "1",
          "docType": "id_front",
          "fileUrl": "https://example.com/id_front.jpg",
          "fileHash": "abc123def456",
          "status": "pending",
          "createdAt": "2024-01-01T00:00:00.000Z"
        }
      ]
    }
  ],
  "requirements": {
    "basic": ["id_front", "selfie"],
    "plus": ["id_front", "id_back", "selfie", "proof_of_address"],
    "premium": ["id_front", "id_back", "selfie", "proof_of_address"]
  }
}
```

---

## Notification APIs

### GET /notifications

Get user notifications.

**Request:**
```http
GET /notifications?limit=20&offset=0
Authorization: Bearer <token>
```

**Query Parameters:**
- `limit` (optional): Items per page (default: 20)
- `offset` (optional): Number of items to skip (default: 0)

**Response:**
```json
{
  "notifications": [
    {
      "id": "1",
      "type": "reward_earned",
      "title": "Reward Earned!",
      "message": "You earned 1.0 OKT for completing daily check-in",
      "data": {
        "campaignId": "1",
        "taskId": "1",
        "amount": "1000000"
      },
      "isRead": false,
      "createdAt": "2024-01-01T00:00:00.000Z"
    }
  ],
  "pagination": {
    "limit": 20,
    "offset": 0,
    "total": 1
  }
}
```

### POST /notifications/:id/read

Mark notification as read.

**Request:**
```http
POST /notifications/1/read
Authorization: Bearer <token>
```

**Response:**
```json
{
  "notification": {
    "id": "1",
    "type": "reward_earned",
    "title": "Reward Earned!",
    "message": "You earned 1.0 OKT for completing daily check-in",
    "data": {
      "campaignId": "1",
      "taskId": "1",
      "amount": "1000000"
    },
    "isRead": true,
    "createdAt": "2024-01-01T00:00:00.000Z",
    "readAt": "2024-01-01T00:00:00.000Z"
  }
}
```

---

## Referral APIs

### GET /referrals/stats

Get referral statistics for the user.

**Request:**
```http
GET /referrals/stats
Authorization: Bearer <token>
```

**Response:**
```json
{
  "stats": {
    "totalReferrals": 5,
    "activeReferrals": 3,
    "totalRewards": "5000000",
    "totalRewardsFormatted": "5.000000",
    "levelStats": [
      {
        "level": 1,
        "count": 3,
        "rewards": "3000000",
        "rewardsFormatted": "3.000000"
      },
      {
        "level": 2,
        "count": 2,
        "rewards": "2000000",
        "rewardsFormatted": "2.000000"
      }
    ]
  }
}
```

### GET /referrals/tree

Get referral tree structure.

**Request:**
```http
GET /referrals/tree?maxDepth=3
Authorization: Bearer <token>
```

**Query Parameters:**
- `maxDepth` (optional): Maximum depth to return (default: 3)

**Response:**
```json
{
  "tree": {
    "userId": "1",
    "refCode": "ABC12345",
    "level": 0,
    "children": [
      {
        "userId": "2",
        "refCode": "XYZ67890",
        "level": 1,
        "joinedAt": "2024-01-01T00:00:00.000Z",
        "children": [
          {
            "userId": "3",
            "refCode": "DEF45678",
            "level": 2,
            "joinedAt": "2024-01-02T00:00:00.000Z",
            "children": []
          }
        ]
      }
    ]
  }
}
```

---

## Webhook APIs

### POST /webhooks

Create a new webhook.

**Request:**
```http
POST /webhooks
Authorization: Bearer <token>
Content-Type: application/json

{
  "target": "https://example.com/webhook",
  "event": "user.created",
  "secret": "webhook-secret-key"
}
```

**Response:**
```json
{
  "webhook": {
    "id": "1",
    "target": "https://example.com/webhook",
    "event": "user.created",
    "hasSecret": true,
    "status": "active",
    "createdAt": "2024-01-01T00:00:00.000Z"
  }
}
```

### GET /webhooks

List user's webhooks.

**Request:**
```http
GET /webhooks?event=user.created
Authorization: Bearer <token>
```

**Query Parameters:**
- `event` (optional): Filter by event type

**Response:**
```json
{
  "webhooks": [
    {
      "id": "1",
      "target": "https://example.com/webhook",
      "event": "user.created",
      "hasSecret": true,
      "status": "active",
      "createdAt": "2024-01-01T00:00:00.000Z"
    }
  ]
}
```

### GET /webhooks/:id/stats

Get webhook delivery statistics.

**Request:**
```http
GET /webhooks/1/stats
Authorization: Bearer <token>
```

**Response:**
```json
{
  "stats": {
    "webhookId": "1",
    "totalDeliveries": 10,
    "successfulDeliveries": 8,
    "failedDeliveries": 2,
    "successRate": 0.8,
    "lastDeliveryAt": "2024-01-01T00:00:00.000Z",
    "recentDeliveries": [
      {
        "id": "1",
        "status": "success",
        "responseCode": 200,
        "deliveredAt": "2024-01-01T00:00:00.000Z"
      }
    ]
  }
}
```

---

## Admin APIs

### GET /admin/campaigns

List all campaigns (admin only).

**Request:**
```http
GET /admin/campaigns?limit=20&offset=0
Authorization: Bearer <admin-token>
```

**Query Parameters:**
- `limit` (optional): Items per page (default: 20)
- `offset` (optional): Number of items to skip (default: 0)

**Response:**
```json
{
  "campaigns": [
    {
      "id": "1",
      "code": "welcome-airdrop",
      "name": "Welcome Airdrop Campaign",
      "type": "airdrop",
      "status": "active",
      "description": "Welcome to Open Kingdom! Complete daily check-ins to earn rewards.",
      "participantCount": 150,
      "totalRewardsDistributed": "150000000",
      "totalRewardsDistributedFormatted": "150.000000",
      "createdAt": "2024-01-01T00:00:00.000Z",
      "updatedAt": "2024-01-01T00:00:00.000Z"
    }
  ],
  "pagination": {
    "limit": 20,
    "offset": 0,
    "total": 1
  }
}
```

### GET /admin/users

List all users (admin only).

**Request:**
```http
GET /admin/users?limit=20&offset=0
Authorization: Bearer <admin-token>
```

**Query Parameters:**
- `limit` (optional): Items per page (default: 20)
- `offset` (optional): Number of items to skip (default: 0)

**Response:**
```json
{
  "users": [
    {
      "id": "1",
      "email": "user@example.com",
      "phone": "+12025550123",
      "status": "active",
      "countryCode": "US",
      "language": "en",
      "refCode": "ABC12345",
      "kycStatus": "verified",
      "kycLevel": "basic",
      "totalRewards": "10000000",
      "totalRewardsFormatted": "10.000000",
      "createdAt": "2024-01-01T00:00:00.000Z"
    }
  ],
  "pagination": {
    "limit": 20,
    "offset": 0,
    "total": 1
  }
}
```

### POST /admin/kyc/:id/review

Review KYC submission (admin only).

**Request:**
```http
POST /admin/kyc/1/review
Authorization: Bearer <admin-token>
Content-Type: application/json

{
  "action": "approve",
  "reason": "Documents look good"
}
```

**Response:**
```json
{
  "kycRequest": {
    "id": "1",
    "userId": "1",
    "level": "basic",
    "status": "approved",
    "submittedAt": "2024-01-01T00:00:00.000Z",
    "reviewedBy": "admin-user-id",
    "reviewedAt": "2024-01-01T00:00:00.000Z",
    "rejectReason": null
  }
}
```

### GET /admin/stats

Get system statistics (admin only).

**Request:**
```http
GET /admin/stats
Authorization: Bearer <admin-token>
```

**Response:**
```json
{
  "stats": {
    "users": {
      "total": 1000,
      "active": 950,
      "verified": 800
    },
    "campaigns": {
      "total": 5,
      "active": 3,
      "completed": 2
    },
    "rewards": {
      "totalDistributed": "1000000000",
      "totalDistributedFormatted": "1000.000000",
      "pendingDistribution": "50000000",
      "pendingDistributionFormatted": "50.000000"
    },
    "kyc": {
      "pending": 10,
      "approved": 800,
      "rejected": 20
    }
  }
}
```

---

## Error Examples

### Validation Error (422)
```json
{
  "message": "Validation failed",
  "code": "VALIDATION_ERROR",
  "details": [
    {
      "field": "email",
      "message": "Invalid email format",
      "code": "invalid_string"
    }
  ]
}
```

### Unauthorized Error (401)
```json
{
  "message": "Invalid or expired token",
  "code": "UNAUTHORIZED"
}
```

### Not Found Error (404)
```json
{
  "message": "Campaign not found",
  "code": "NOT_FOUND"
}
```

### Forbidden Error (403)
```json
{
  "message": "Insufficient permissions",
  "code": "FORBIDDEN"
}
```

### Bad Request Error (400)
```json
{
  "message": "Invalid request parameters",
  "code": "BAD_REQUEST"
}
```

### Internal Server Error (500)
```json
{
  "message": "Internal Server Error",
  "code": "INTERNAL_SERVER_ERROR"
}
```

---

## Rate Limiting

API endpoints may be rate limited. When rate limits are exceeded, the API will return:

```json
{
  "message": "Rate limit exceeded",
  "code": "RATE_LIMIT_EXCEEDED"
}
```

With HTTP status code `429 Too Many Requests`.

---

## Webhook Events

The system supports the following webhook events:

- `user.created` - New user registration
- `user.updated` - User profile update
- `campaign.joined` - User joined a campaign
- `task.completed` - User completed a task
- `reward.earned` - User earned a reward
- `kyc.submitted` - KYC documents submitted
- `kyc.approved` - KYC approved
- `kyc.rejected` - KYC rejected

Webhook payloads include the event type, timestamp, and relevant data.
