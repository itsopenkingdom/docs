# API Documentation - Open Kingdom Backend

> **Base URL**: `http://localhost:3000` (local) | `https://api.openkingdom.com` (production)  
> **Version**: 1.0.0  
> **Last Updated**: November 27, 2025

## Table of Contents

- [Authentication](#authentication)
- [Health Check](#health-check)
- [User Management](#user-management)
- [Home & Dashboard](#home--dashboard)
- [Campaigns](#campaigns)
- [Wallets](#wallets)
- [Referrals](#referrals)
- [Airdrops](#airdrops)
- [KYC](#kyc)
- [Notifications](#notifications)
- [Error Codes](#error-codes)

---

## Authentication

### Common Headers

| Header | Value | Required | Description |
|--------|-------|----------|-------------|
| `Content-Type` | `application/json` | Yes | Request content type |
| `Authorization` | `Bearer {token}` | Auth required | JWT access token |

---

## Health Check

### 1. Health Check

**Endpoint**: `GET /health`  
**Description**: Check API server health status  
**Authentication**: No

**Response** (200):
```json
{
  "status": "healthy",
  "timestamp": "2025-11-27T09:45:03.149Z",
  "service": "open-kingdom-backend",
  "version": "1.0.0"
}
```

---

## Authentication

### 2. Send Verification Code

**Endpoint**: `POST /auth/send-verification-code`  
**Description**: Send email verification code for registration or password reset  
**Authentication**: No

**Request Body**:
```json
{
  "email": "user@example.com",
  "type": "registration"  // "registration" | "password_reset"
}
```

**Response** (200):
```json
{
  "message": "Verification code sent successfully",
  "expiresAt": "2025-11-27T10:00:00.000Z"
}
```

**Validation Rules**:
- `email`: Valid email format, max 255 characters
- `type`: Must be "registration" or "password_reset"

---

### 3. Verify Code

**Endpoint**: `POST /auth/verify-code`  
**Description**: Verify email verification code  
**Authentication**: No

**Request Body**:
```json
{
  "email": "user@example.com",
  "code": "123456",
  "type": "registration"
}
```

**Response** (200):
```json
{
  "verified": true,
  "verificationToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "expiresAt": "2025-11-27T10:30:00.000Z"
}
```

**Validation Rules**:
- `code`: Exactly 6 characters
- `type`: Must be "registration" or "password_reset"

**Error Codes**:
- `AUTH_00001`: Invalid or expired verification code
- `AUTH_00002`: Too many failed attempts

---

### 4. Complete Registration

**Endpoint**: `POST /auth/complete-registration`  
**Description**: Complete user registration after email verification  
**Authentication**: No

**Request Body**:
```json
{
  "email": "user@example.com",
  "fullName": "John Doe",
  "countryCode": "US",
  "password": "Password123!",
  "confirmPassword": "Password123!",
  "refCode": "ABC12345",  // optional
  "verificationToken": "eyJhbGci..."
}
```

**Response** (201):
```json
{
  "user": {
    "id": "123456789",
    "email": "user@example.com",
    "fullName": "John Doe",
    "countryCode": "US",
    "createdAt": "2025-11-27T09:45:00.000Z"
  },
  "tokens": {
    "accessToken": "eyJhbGci...",
    "refreshToken": "eyJhbGci...",
    "expiresIn": 3600
  }
}
```

**Validation Rules**:
- `fullName`: Max 50 characters, letters and spaces only
- `password`: Min 8 characters, must contain uppercase, lowercase, and number
- `countryCode`: Exactly 2 characters (ISO 3166-1 alpha-2)
- `refCode`: Optional, exactly 8 characters if provided

---

### 5. Login

**Endpoint**: `POST /auth/login`  
**Description**: User login with email and password  
**Authentication**: No

**Request Body**:
```json
{
  "email": "user@example.com",
  "password": "Password123!"
}
```

**Response** (200):
```json
{
  "user": {
    "id": "123456789",
    "email": "user@example.com",
    "fullName": "John Doe",
    "twoFactorEnabled": false
  },
  "tokens": {
    "accessToken": "eyJhbGci...",
    "refreshToken": "eyJhbGci...",
    "expiresIn": 3600
  },
  "tempToken": null  // Only present if 2FA is enabled
}
```

**Response with 2FA** (200):
```json
{
  "user": { ... },
  "tokens": null,
  "tempToken": "temp_eyJhbGci..."  // Used for 2FA verification
}
```

**Error Codes**:
- `USR_00003`: Incorrect email or password
- `USR_00004`: Account is locked
- `USR_00005`: Account is deactivated

---

### 6. Refresh Token

**Endpoint**: `POST /auth/refresh`  
**Description**: Refresh access token using refresh token  
**Authentication**: No

**Request Body**:
```json
{
  "refreshToken": "eyJhbGci..."
}
```

**Response** (200):
```json
{
  "accessToken": "eyJhbGci...",
  "refreshToken": "eyJhbGci...",
  "expiresIn": 3600
}
```

---

### 7. Logout

**Endpoint**: `POST /auth/logout`  
**Description**: Logout and invalidate tokens  
**Authentication**: Yes

**Response** (200):
```json
{
  "message": "Logged out successfully"
}
```

---

### 8. Google OAuth

**Endpoint**: `POST /auth/google`  
**Description**: Login/Register with Google  
**Authentication**: No

**Request Body**:
```json
{
  "idToken": "google_id_token_here",
  "type": "login"  // "login" | "register"
}
```

**Response** (200): Same as login response

---

### 9. Facebook OAuth

**Endpoint**: `POST /auth/facebook`  
**Description**: Login/Register with Facebook  
**Authentication**: No

**Request Body**:
```json
{
  "accessToken": "facebook_access_token",
  "userID": "facebook_user_id",
  "type": "login"
}
```

**Response** (200): Same as login response

---

### 10. Apple OAuth

**Endpoint**: `POST /auth/apple`  
**Description**: Login/Register with Apple  
**Authentication**: No

**Request Body**:
```json
{
  "idToken": "apple_id_token",
  "type": "login"
}
```

**Response** (200): Same as login response

---

### 11. Complete SSO Registration

**Endpoint**: `POST /auth/complete-sso-registration`  
**Description**: Complete registration for SSO users (Google/Facebook/Apple)  
**Authentication**: No

**Request Body**:
```json
{
  "email": "user@example.com",
  "fullName": "John Doe",
  "countryCode": "US",
  "refCode": "ABC12345"  // optional
}
```

**Response** (201): Same as complete registration response

---

### 12. Forgot Password

**Endpoint**: `POST /auth/forgot-password`  
**Description**: Request password reset email  
**Authentication**: No

**Request Body**:
```json
{
  "email": "user@example.com"
}
```

**Response** (200):
```json
{
  "message": "Password reset email sent"
}
```

---

### 13. Reset Password

**Endpoint**: `POST /auth/reset-password`  
**Description**: Reset password with verification token  
**Authentication**: No

**Request Body**:
```json
{
  "email": "user@example.com",
  "newPassword": "NewPassword123!",
  "verificationToken": "eyJhbGci..."
}
```

**Response** (200):
```json
{
  "message": "Password reset successfully"
}
```

---

### 14. Verify 2FA

**Endpoint**: `POST /auth/verify-2fa`  
**Description**: Verify 2FA code during login  
**Authentication**: No (uses temp token)

**Request Body**:
```json
{
  "totp": "123456",
  "token": "temp_eyJhbGci..."
}
```

**Response** (200): Same as login response

---

### 15. Get QR Code for 2FA

**Endpoint**: `GET /auth/qr2fa`  
**Description**: Get QR code for 2FA setup  
**Authentication**: Yes

**Response** (200):
```json
{
  "qrCode": "data:image/png;base64,iVBORw0KGgo...",
  "secret": "JBSWY3DPEHPK3PXP"
}
```

---

### 16. Get Current User (Auth)

**Endpoint**: `GET /auth/me`  
**Description**: Get current authenticated user info  
**Authentication**: Yes

**Response** (200):
```json
{
  "id": "123456789",
  "email": "user@example.com",
  "fullName": "John Doe",
  "phone": "+1234567890",
  "countryCode": "US",
  "language": "en",
  "emailVerified": true,
  "twoFactorEnabled": false,
  "kycStatus": "pending",
  "createdAt": "2025-11-27T09:45:00.000Z"
}
```

---

## User Management

### 17. Get Current User

**Endpoint**: `GET /users/me`  
**Description**: Get current user profile  
**Authentication**: Yes

**Response** (200): Same as `/auth/me`

---

### 18. Update Profile

**Endpoint**: `PUT /users/profile`  
**Description**: Update user profile information  
**Authentication**: Yes

**Request Body**:
```json
{
  "firstName": "John",
  "lastName": "Doe",
  "phone": "+1234567890",
  "countryCode": "US",
  "language": "en",
  "dob": "1990-01-01T00:00:00.000Z",
  "address": {
    "street": "123 Main St",
    "city": "New York",
    "state": "NY",
    "zipCode": "10001"
  }
}
```

**Response** (200):
```json
{
  "message": "Profile updated successfully",
  "user": { ... }
}
```

---

### 19. Change Password

**Endpoint**: `PUT /users/password`  
**Description**: Change user password  
**Authentication**: Yes

**Request Body**:
```json
{
  "currentPassword": "OldPassword123!",
  "newPassword": "NewPassword123!"
}
```

**Response** (200):
```json
{
  "message": "Password changed successfully"
}
```

**Error Codes**:
- `USR_00006`: Current password is incorrect

---

### 20. Enable 2FA

**Endpoint**: `POST /users/security/2fa/enable`  
**Description**: Enable two-factor authentication  
**Authentication**: Yes

**Response** (200):
```json
{
  "qrCode": "data:image/png;base64,...",
  "secret": "JBSWY3DPEHPK3PXP",
  "backupCodes": ["12345678", "87654321", ...]
}
```

---

### 21. Verify Enable 2FA

**Endpoint**: `POST /users/security/2fa/verify-enable`  
**Description**: Verify and confirm 2FA enablement  
**Authentication**: Yes

**Request Body**:
```json
{
  "totp": "123456",
  "secret": "JBSWY3DPEHPK3PXP"
}
```

**Response** (200):
```json
{
  "message": "2FA enabled successfully",
  "backupCodes": ["12345678", "87654321", ...]
}
```

---

### 22. Disable 2FA

**Endpoint**: `POST /users/security/2fa/disable`  
**Description**: Disable two-factor authentication  
**Authentication**: Yes

**Request Body**:
```json
{
  "totp": "123456"
}
```

**Response** (200):
```json
{
  "message": "2FA disabled successfully"
}
```

---

### 23. Get 2FA Status

**Endpoint**: `GET /users/security/2fa/status`  
**Description**: Get 2FA status for current user  
**Authentication**: Yes

**Response** (200):
```json
{
  "enabled": false,
  "method": null
}
```

---

### 24. Get Sessions

**Endpoint**: `GET /profile/sessions`  
**Description**: Get all active sessions for current user  
**Authentication**: Yes

**Response** (200):
```json
{
  "sessions": [
    {
      "id": "session_123",
      "deviceInfo": "Chrome on Windows",
      "ipAddress": "192.168.1.1",
      "lastActiveAt": "2025-11-27T09:45:00.000Z",
      "createdAt": "2025-11-27T08:00:00.000Z",
      "isCurrent": true
    }
  ]
}
```

---

### 25. Revoke Session

**Endpoint**: `DELETE /profile/sessions/{id}`  
**Description**: Revoke a specific session  
**Authentication**: Yes

**Path Parameters**:
- `id`: Session ID to revoke

**Response** (200):
```json
{
  "message": "Session revoked successfully"
}
```

---

### 26. Deactivate Account

**Endpoint**: `POST /account/deactivate`  
**Description**: Temporarily deactivate account  
**Authentication**: Yes

**Request Body**:
```json
{
  "reason": "Taking a break",
  "password": "Password123!"
}
```

**Response** (200):
```json
{
  "message": "Account deactivated successfully"
}
```

---

### 27. Delete Account

**Endpoint**: `POST /account/delete`  
**Description**: Permanently delete account  
**Authentication**: Yes

**Request Body**:
```json
{
  "reason": "No longer need the service",
  "password": "Password123!"
}
```

**Response** (200):
```json
{
  "message": "Account deletion scheduled",
  "deletionDate": "2025-12-27T09:45:00.000Z"
}
```

---

### 28. Export Data

**Endpoint**: `GET /account/export`  
**Description**: Export all user data (GDPR compliance)  
**Authentication**: Yes

**Response** (200):
```json
{
  "exportUrl": "https://storage.example.com/exports/user_123.zip",
  "expiresAt": "2025-11-28T09:45:00.000Z"
}
```

---

## Home & Dashboard

### 29. Home Dashboard

**Endpoint**: `GET /home`  
**Description**: Get home dashboard data  
**Authentication**: Yes

**Response** (200):
```json
{
  "user": { ... },
  "stats": {
    "totalRewards": "1000.00",
    "totalReferrals": 10,
    "activeCampaigns": 5
  },
  "recentActivity": [...]
}
```

---

### 30. Rewards Today

**Endpoint**: `GET /rewards/today`  
**Description**: Get rewards earned today  
**Authentication**: Yes

**Response** (200):
```json
{
  "totalToday": "50.00",
  "rewards": [
    {
      "id": "123",
      "amount": "10.00",
      "tokenSymbol": "OKT",
      "source": "referral",
      "createdAt": "2025-11-27T09:00:00.000Z"
    }
  ]
}
```

---

### 31. Rewards Pending

**Endpoint**: `GET /rewards/pending`  
**Description**: Get pending rewards  
**Authentication**: Yes

**Response** (200):
```json
{
  "totalPending": "150.00",
  "rewards": [...]
}
```

---

## Campaigns

### 32. List Campaigns

**Endpoint**: `GET /campaigns`  
**Description**: Get all campaigns  
**Authentication**: Yes

**Query Parameters**:
- `page`: Page number (default: 1)
- `limit`: Items per page (default: 20, max: 100)
- `status`: Filter by status ("active" | "upcoming" | "ended")

**Response** (200):
```json
{
  "campaigns": [
    {
      "id": "123",
      "code": "welcome-campaign",
      "name": "Welcome Campaign",
      "type": "airdrop",
      "description": "Get started with rewards",
      "bannerUrl": "https://...",
      "startAt": "2025-11-01T00:00:00.000Z",
      "endAt": "2025-12-31T23:59:59.000Z",
      "status": "active",
      "reward": {
        "tokenId": "1",
        "tokenSymbol": "OKT",
        "amount": "100.00"
      }
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 50,
    "totalPages": 3
  }
}
```

---

### 33. Get Campaign

**Endpoint**: `GET /campaigns/{id}`  
**Description**: Get campaign details  
**Authentication**: Yes

**Path Parameters**:
- `id`: Campaign ID

**Response** (200):
```json
{
  "id": "123",
  "code": "welcome-campaign",
  "name": "Welcome Campaign",
  "type": "airdrop",
  "description": "Get started with rewards",
  "bannerUrl": "https://...",
  "startAt": "2025-11-01T00:00:00.000Z",
  "endAt": "2025-12-31T23:59:59.000Z",
  "status": "active",
  "reward": { ... },
  "tasks": [...],
  "userProgress": {
    "joined": true,
    "completedTasks": 5,
    "totalTasks": 10,
    "earnedRewards": "50.00"
  }
}
```

---

### 34. Join Campaign

**Endpoint**: `POST /campaigns/{id}/join`  
**Description**: Join a campaign  
**Authentication**: Yes

**Path Parameters**:
- `id`: Campaign ID

**Response** (200):
```json
{
  "message": "Successfully joined campaign",
  "campaign": { ... }
}
```

---

### 35. Get Campaign Tasks

**Endpoint**: `GET /campaigns/{id}/tasks`  
**Description**: Get all tasks for a campaign  
**Authentication**: Yes

**Path Parameters**:
- `id`: Campaign ID

**Response** (200):
```json
{
  "tasks": [
    {
      "id": "task_123",
      "title": "Follow on Twitter",
      "description": "Follow our Twitter account",
      "type": "social",
      "reward": "10.00",
      "status": "incomplete",
      "completedAt": null
    }
  ]
}
```

---

### 36. Complete Task

**Endpoint**: `POST /campaigns/{id}/tasks/{taskId}/complete`  
**Description**: Mark a task as completed  
**Authentication**: Yes

**Path Parameters**:
- `id`: Campaign ID
- `taskId`: Task ID

**Request Body**:
```json
{
  "evidence": {
    "twitterUsername": "@johndoe",
    "screenshotUrl": "https://..."
  }
}
```

**Response** (200):
```json
{
  "message": "Task completed successfully",
  "reward": {
    "amount": "10.00",
    "tokenSymbol": "OKT"
  }
}
```

---

### 37. Leave Campaign

**Endpoint**: `POST /campaigns/{id}/leave`  
**Description**: Leave a campaign  
**Authentication**: Yes

**Response** (200):
```json
{
  "message": "Successfully left campaign"
}
```

---

### 38. Get Campaign Progress

**Endpoint**: `GET /campaigns/{id}/progress`  
**Description**: Get user progress in a campaign  
**Authentication**: Yes

**Response** (200):
```json
{
  "campaignId": "123",
  "joined": true,
  "completedTasks": 5,
  "totalTasks": 10,
  "earnedRewards": "50.00",
  "tasks": [...]
}
```

---

### 39. Get Campaign Rewards

**Endpoint**: `GET /campaigns/{id}/rewards`  
**Description**: Get rewards earned from a campaign  
**Authentication**: Yes

**Response** (200):
```json
{
  "campaignId": "123",
  "totalRewards": "50.00",
  "rewards": [...]
}
```

---

## Wallets

### 40. Get Balance

**Endpoint**: `GET /wallets/balance`  
**Description**: Get wallet balance  
**Authentication**: Yes

**Response** (200):
```json
{
  "balances": [
    {
      "tokenId": "1",
      "tokenSymbol": "OKT",
      "tokenName": "Open Kingdom Token",
      "balance": "1000.50",
      "lockedBalance": "100.00",
      "availableBalance": "900.50"
    }
  ]
}
```

---

### 41. Get Transactions

**Endpoint**: `GET /wallets/transactions`  
**Description**: Get transaction history  
**Authentication**: Yes

**Query Parameters**:
- `page`: Page number
- `limit`: Items per page
- `type`: Filter by type ("transfer" | "withdraw" | "reward" | "deposit")

**Response** (200):
```json
{
  "transactions": [
    {
      "id": "tx_123",
      "type": "transfer",
      "amount": "50.00",
      "tokenSymbol": "OKT",
      "from": "user_456",
      "to": "user_789",
      "status": "completed",
      "createdAt": "2025-11-27T09:00:00.000Z"
    }
  ],
  "pagination": { ... }
}
```

---

### 42. Transfer

**Endpoint**: `POST /wallets/transfer`  
**Description**: Transfer tokens to another user  
**Authentication**: Yes

**Request Body**:
```json
{
  "toUserId": "123456789",
  "tokenId": "1",
  "amount": "50.00",
  "note": "Payment for services"
}
```

**Response** (200):
```json
{
  "message": "Transfer successful",
  "transaction": {
    "id": "tx_123",
    "amount": "50.00",
    "tokenSymbol": "OKT",
    "to": "user_789",
    "status": "completed"
  }
}
```

---

### 43. Withdraw

**Endpoint**: `POST /wallets/withdraw`  
**Description**: Withdraw tokens to external wallet  
**Authentication**: Yes

**Request Body**:
```json
{
  "tokenId": "1",
  "amount": "100.00",
  "destination": "0x742d35Cc6634C0532925a3b844Bc9e7595f0bEb",
  "chainId": "1",
  "feeTokenId": "1"
}
```

**Response** (200):
```json
{
  "message": "Withdrawal request submitted",
  "request": {
    "id": "wr_123",
    "status": "pending",
    "estimatedTime": "2025-11-27T10:00:00.000Z"
  }
}
```

---

### 44. Get Transaction Details

**Endpoint**: `GET /wallets/transactions/{id}`  
**Description**: Get detailed transaction information  
**Authentication**: Yes

**Response** (200):  
```json
{
  "id": "tx_123",
  "type": "transfer",
  "amount": "50.00",
  "tokenSymbol": "OKT",
  "from": { ... },
  "to": { ... },
  "status": "completed",
  "txHash": "0x...",
  "createdAt": "2025-11-27T09:00:00.000Z"
}
```

---

### 45. Get Withdraw Requests

**Endpoint**: `GET /wallets/withdraw/requests`  
**Description**: Get all withdrawal requests  
**Authentication**: Yes

**Response** (200):
```json
{
  "requests": [
    {
      "id": "wr_123",
      "amount": "100.00",
      "tokenSymbol": "OKT",
      "destination": "0x742d...",
      "status": "pending",
      "createdAt": "2025-11-27T09:00:00.000Z"
    }
  ]
}
```

---

### 46. Get Withdraw Request Details

**Endpoint**: `GET /wallets/withdraw/requests/{id}`  
**Description**: Get withdrawal request details  
**Authentication**: Yes

**Response** (200): Similar to withdraw request object with more details

---

## Referrals

### 47. Get Referral Code

**Endpoint**: `GET /referrals/code`  
**Description**: Get user's referral code  
**Authentication**: Yes

**Response** (200):
```json
{
  "code": "ABC12345",
  "referralLink": "https://app.openkingdom.com/ref/ABC12345",
  "qrCode": "data:image/png;base64,..."
}
```

---

### 48. Update Referral Code

**Endpoint**: `PUT /referrals/code`  
**Description**: Update custom referral code  
**Authentication**: Yes

**Request Body**:
```json
{
  "newCode": "JOHNDOE1"
}
```

**Response** (200):
```json
{
  "message": "Referral code updated",
  "code": "JOHNDOE1"
}
```

---

### 49. Get Referral Members

**Endpoint**: `GET /referrals/members`  
**Description**: Get list of referred users  
**Authentication**: Yes

**Query Parameters**:
- `page`: Page number
- `limit`: Items per page
- `level`: Filter by level (1-3)

**Response** (200):
```json
{
  "members": [
    {
      "userId": "123",
      "fullName": "Jane Doe",
      "level": 1,
      "joinedAt": "2025-11-20T09:00:00.000Z",
      "totalRewardsGenerated": "50.00"
    }
  ],
  "stats": {
    "total": 10,
    "level1": 5,
    "level2": 3,
    "level3": 2
  }
}
```

---

### 50. Get Referral Rewards

**Endpoint**: `GET /referrals/rewards`  
**Description**: Get all referral rewards  
**Authentication**: Yes

**Response** (200):
```json
{
  "totalRewards": "500.00",
  "rewards": [
    {
      "id": "123",
      "amount": "10.00",
      "tokenSymbol": "OKT",
      "fromUser": "user_456",
      "level": 1,
      "createdAt": "2025-11-27T09:00:00.000Z"
    }
  ]
}
```

---

### 51. Get Referral Rewards Today

**Endpoint**: `GET /referrals/rewards/today`  
**Description**: Get referral rewards earned today  
**Authentication**: Yes

**Response** (200): Similar to referral rewards response filtered by today

---

### 52. Share Referral

**Endpoint**: `POST /referrals/share`  
**Description**: Generate shareable referral content  
**Authentication**: Yes

**Request Body**:
```json
{
  "platform": "twitter" // "twitter" | "facebook" | "telegram" | "whatsapp"
}
```

**Response** (200):
```json
{
  "shareUrl": "https://twitter.com/intent/tweet?text=...",
  "message": "Join me on Open Kingdom! Use my code: ABC12345"
}
```

---

### 53. Get Referral Tree

**Endpoint**: `GET /referrals/tree`  
**Description**: Get referral tree structure  
**Authentication**: Yes

**Query Parameters**:
- `maxDepth`: Maximum depth to retrieve (default: 3)

**Response** (200):
```json
{
  "tree": {
    "userId": "123",
    "fullName": "John Doe",
    "level": 0,
    "children": [
      {
        "userId": "456",
        "fullName": "Jane Doe",
        "level": 1,
        "children": [...]
      }
    ]
  }
}
```

---

### 54. Get Referral Stats

**Endpoint**: `GET /referrals/stats`  
**Description**: Get referral statistics  
**Authentication**: Yes

**Query Parameters**:
- `level`: Filter by level (1-3)
- `startDate`: Start date for stats
- `endDate`: End date for stats

**Response** (200):
```json
{
  "totalReferrals": 10,
  "level1": 5,
  "level2": 3,
  "level3": 2,
  "totalRewards": "500.00",
  "thisMonth": {
    "newReferrals": 2,
    "rewards": "50.00"
  }
}
```

---

## Airdrops

### 55. Checkin

**Endpoint**: `POST /airdrops/checkin`  
**Description**: Daily checkin for airdrop  
**Authentication**: Yes

**Request Body**:
```json
{
  "campaignId": "123"
}
```

**Response** (200):
```json
{
  "message": "Checkin successful",
  "reward": {
    "amount": "10.00",
    "tokenSymbol": "OKT"
  },
  "streak": 5,
  "nextCheckinAt": "2025-11-28T00:00:00.000Z"
}
```

---

### 56. Get Airdrop Stats

**Endpoint**: `GET /airdrops/stats`  
**Description**: Get airdrop statistics  
**Authentication**: Yes

**Response** (200):
```json
{
  "totalCheckins": 30,
  "currentStreak": 5,
  "longestStreak": 15,
  "totalRewards": "300.00",
  "lastCheckinAt": "2025-11-27T09:00:00.000Z"
}
```

---

## KYC

### 57. Submit KYC

**Endpoint**: `POST /kyc/submit`  
**Description**: Submit KYC verification request  
**Authentication**: Yes

**Request Body**:
```json
{
  "level": "basic",  // "basic" | "plus" | "premium"
  "documents": [
    {
      "docType": "id_front",
      "fileUrl": "https://storage.example.com/docs/id-front.jpg",
      "fileHash": "sha256:..."
    },
    {
      "docType": "id_back",
      "fileUrl": "https://storage.example.com/docs/id-back.jpg"
    },
    {
      "docType": "selfie",
      "fileUrl": "https://storage.example.com/docs/selfie.jpg"
    }
  ]
}
```

**Response** (200):
```json
{
  "message": "KYC submitted successfully",
  "requestId": "kyc_123",
  "status": "pending",
  "estimatedReviewTime": "24-48 hours"
}
```

---

### 58. Get KYC Status

**Endpoint**: `GET /kyc/status`  
**Description**: Get KYC verification status  
**Authentication**: Yes

**Response** (200):
```json
{
  "status": "pending",  // "none" | "pending" | "approved" | "rejected"
  "level": "basic",
  "requestId": "kyc_123",
  "submittedAt": "2025-11-27T09:00:00.000Z",
  "reviewedAt": null,
  "rejectionReason": null
}
```

---

## Notifications

### 59. List Notifications

**Endpoint**: `GET /notifications`  
**Description**: Get all notifications  
**Authentication**: Yes

**Query Parameters**:
- `page`: Page number
- `limit`: Items per page
- `unreadOnly`: Filter unread only (boolean)

**Response** (200):
```json
{
  "notifications": [
    {
      "id": "notif_123",
      "type": "reward",
      "title": "Reward Received",
      "message": "You received 10 OKT",
      "data": {
        "amount": "10.00",
        "tokenSymbol": "OKT"
      },
      "read": false,
      "createdAt": "2025-11-27T09:00:00.000Z"
    }
  ],
  "pagination": { ... }
}
```

---

### 60. Mark Notification as Read

**Endpoint**: `PUT /notifications/{id}/read`  
**Description**: Mark a notification as read  
**Authentication**: Yes

**Response** (200):
```json
{
  "message": "Notification marked as read"
}
```

---

### 61. Get Unread Count

**Endpoint**: `GET /notifications/unread-count`  
**Description**: Get count of unread notifications  
**Authentication**: Yes

**Response** (200):
```json
{
  "count": 5
}
```

---

### 62. Mark All as Read

**Endpoint**: `PUT /notifications/read-all`  
**Description**: Mark all notifications as read  
**Authentication**: Yes

**Response** (200):
```json
{
  "message": "All notifications marked as read",
  "count": 5
}
```

---

### 63. Delete Notification

**Endpoint**: `DELETE /notifications/{id}`  
**Description**: Delete a notification  
**Authentication**: Yes

**Response** (200):
```json
{
  "message": "Notification deleted successfully"
}
```

---

## Error Codes

### Authentication Errors

| Code | Message | Description |
|------|---------|-------------|
| `AUTH_00001` | Invalid or expired verification code | Verification code is wrong or expired |
| `AUTH_00002` | Too many failed attempts | Rate limit exceeded |
| `AUTH_00003` | Invalid token | Access token is invalid or expired |
| `AUTH_00004` | Refresh token required | Refresh token missing |

### User Errors

| Code | Message | Description |
|------|---------|-------------|
| `USR_00001` | User already exists | Email already registered |
| `USR_00002` | User not found | User does not exist |
| `USR_00003` | Incorrect email or password | Login credentials invalid |
| `USR_00004` | Account is locked | Too many failed login attempts |
| `USR_00005` | Account is deactivated | Account has been deactivated |
| `USR_00006` | Current password is incorrect | Wrong current password |

### Campaign Errors

| Code | Message | Description |
|------|---------|-------------|
| `CMP_00001` | Campaign not found | Campaign does not exist |
| `CMP_00002` | Campaign not active | Campaign is not currently active |
| `CMP_00003` | Already joined | User already joined this campaign |
| `CMP_00004` | Task not found | Task does not exist |
| `CMP_00005` | Task already completed | Task already marked as complete |

### Wallet Errors

| Code | Message | Description |
|------|---------|-------------|
| `WLT_00001` | Insufficient balance | Not enough balance for transaction |
| `WLT_00002` | Invalid recipient | Recipient user not found |
| `WLT_00003` | Minimum amount not met | Amount below minimum threshold |
| `WLT_00004` | Maximum amount exceeded | Amount above maximum threshold |

### General Errors

| Code | Message | Description |
|------|---------|-------------|
| `GEN_00001` | Validation error | Request validation failed |
| `GEN_00002` | Internal server error | Unexpected server error |
| `GEN_00003` | Resource not found | Requested resource not found |
| `GEN_00004` | Unauthorized | Authentication required |
| `GEN_00005` | Forbidden | Insufficient permissions |

---

## Rate Limiting

**Rate Limit**: 100 requests per minute per IP  
**Rate Limit Headers**:
- `X-RateLimit-Limit`: Maximum requests allowed
- `X-RateLimit-Remaining`: Remaining requests
- `X-RateLimit-Reset`: Unix timestamp when limit resets

**Response** (429):
```json
{
  "message": "Rate limit exceeded",
  "code": "RATE_LIMIT_EXCEEDED",
  "retryAfter": 60
}
```

---

## Pagination

All list endpoints support pagination with consistent parameters:

**Query Parameters**:
- `page`: Page number (default: 1, min: 1)
- `limit`: Items per page (default: 20, min: 1, max: 100)

**Response Format**:
```json
{
  "data": [...],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 100,
    "totalPages": 5,
    "hasNext": true,
    "hasPrevious": false
  }
}
```

---

**End of API Documentation**
