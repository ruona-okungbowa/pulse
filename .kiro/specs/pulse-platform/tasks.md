# Implementation Plan

- [ ] 1. Set up project structure and development environment

  - Initialize Expo project with TypeScript for mobile app
  - Initialize Next.js 14 project with App Router and TypeScript for web dashboard
  - Set up Supabase project and configure environment variables
  - Configure ESLint, Prettier, and TypeScript configs for both projects
  - Set up monorepo structure with shared types package
  - _Requirements: All requirements depend on proper project setup_

- [ ] 2. Implement Supabase database schema and security
- [ ] 2.1 Create core database tables

  - Write SQL migration for profiles, teams, check_ins, streaks tables
  - Write SQL migration for blockers table with vector extension for embeddings
  - Write SQL migration for burnout_signals and manager_alerts tables
  - Write SQL migration for slack_workspaces table
  - _Requirements: 1.1, 1.2, 2.1, 10.1, 10.3_

- [ ] 2.2 Implement Row Level Security policies

  - Create RLS policies for profiles table (team members can view each other)
  - Create RLS policies for check_ins table (users insert own, team views all)
  - Create RLS policies for manager_alerts table (only managers view their alerts)
  - Create RLS policies for burnout_signals table (managers only)
  - _Requirements: 14.2, 10.4_

- [ ] 2.3 Create database indexes for performance

  - Add indexes on check_ins (user_id, team_id, submitted_at)
  - Add indexes on blockers (user_id, created_at)
  - Add indexes on burnout_signals (user_id, detected_at)
  - Add indexes on manager_alerts (manager_id, created_at, is_dismissed)
  - _Requirements: 13.1, 13.2_

- [ ] 2.4 Create database functions and triggers

  - Write function to calculate team health score from recent check-ins
  - Write function to update streak on check-in insert
  - Write trigger to call analyze-checkin edge function on check_in insert
  - Write function for generate_1on1_prep RPC call
  - _Requirements: 7.1, 12.1, 12.2, 7.5_

- [ ] 3. Implement authentication and team management
- [ ] 3.1 Set up Supabase Auth integration

  - Configure Supabase Auth with email/password provider
  - Implement sign up flow with email verification
  - Implement login flow with JWT token management
  - Implement password reset flow
  - _Requirements: 10.2, 10.3_

- [ ] 3.2 Create team invitation system

  - Write API route to generate invitation links with 7-day expiration
  - Write API route to validate invitation tokens
  - Implement email sending via SendGrid for invitations
  - Create invitation acceptance flow that creates profile with team_id
  - _Requirements: 10.1, 10.2_

- [ ] 3.3 Implement role-based access control

  - Create middleware to check user role from profiles table
  - Implement role guards for manager-only routes (web dashboard)
  - Implement role guards for manager-only screens (mobile app)
  - Display appropriate error messages for unauthorized access
  - _Requirements: 10.4_

- [ ]\* 3.4 Implement account deletion and data anonymization

  - Write API route to handle account deletion requests
  - Implement background job to anonymize check-in data within 24 hours
  - Update RLS policies to handle anonymized data
  - _Requirements: 10.5, 14.4_

- [ ] 4. Build mobile app check-in flow
- [ ] 4.1 Create check-in navigation and state management

  - Set up React Navigation stack for check-in flow
  - Create Zustand store for check-in draft state
  - Implement AsyncStorage persistence for offline drafts
  - Create check-in form validation logic
  - _Requirements: 1.1, 1.6, 1.7_

- [ ] 4.2 Implement workload and clarity selection screens

  - Create WorkloadScreen with 3 large button options
  - Create ClarityScreen with 3 large button options
  - Implement navigation between screens with state persistence
  - Add accessibility labels for screen readers
  - _Requirements: 1.2, 1.3, 15.1_

- [ ] 4.3 Implement focus and blocker input screens

  - Create FocusScreen with text input (5-500 characters)
  - Add quick pick suggestions for common focus areas
  - Create BlockerScreen with text input and "Nothing blocking me" option
  - Implement character count display and validation
  - _Requirements: 1.4, 1.5_

- [ ] 4.4 Implement check-in submission and celebration

  - Create React Query mutation for check-in submission
  - Implement optimistic updates for instant feedback
  - Create CelebrationScreen with streak display
  - Add animation for streak milestones (7, 14, 30 days)
  - Handle submission errors with retry logic
  - _Requirements: 1.6, 1.8, 12.3_

- [ ] 4.5 Implement offline support for check-ins

  - Detect network connectivity status
  - Queue check-ins locally when offline using AsyncStorage
  - Implement background sync when connection restored
  - Show offline indicator in UI
  - _Requirements: Error handling for network errors_

- [ ] 5. Build mobile app team pulse visualization
- [ ] 5.1 Create team pulse components

  - Build CompletionRing component with circular progress
  - Build WorkloadIndicator with color-coded display
  - Build TrendChart component with 7-day sparkline
  - Implement responsive layout for different screen sizes
  - _Requirements: 6.2, 6.3_

- [ ] 5.2 Implement real-time updates for team pulse

  - Subscribe to Supabase Realtime channel for team check-ins
  - Update local state on INSERT events
  - Implement smooth animations with React Native Reanimated
  - Handle subscription errors with auto-reconnect
  - _Requirements: 6.1, 6.4, 13.1_

- [ ] 5.3 Implement data privacy for team pulse

  - Aggregate workload data without showing individual names
  - Show anonymized metrics for teams smaller than 5 people
  - Display only completion rate and average workload
  - _Requirements: 6.5, 14.3_

- [ ] 6. Build mobile app manager dashboard
- [ ] 6.1 Create manager navigation and layout

  - Set up tab navigation for Team, Alerts, Insights tabs
  - Implement pull-to-refresh for data updates
  - Create role-based conditional rendering (managers only)
  - Add loading states and error boundaries
  - _Requirements: 7.1, 7.2_

- [ ] 6.2 Implement team members list view

  - Fetch team members with health scores from Supabase
  - Display scrollable list with name, avatar, health score, trend
  - Implement tap to view member detail sheet
  - Show last check-in time and active signals count
  - _Requirements: 7.2_

- [ ] 6.3 Implement alerts tab

  - Fetch manager alerts from Supabase with real-time updates
  - Display alert cards with priority badges
  - Implement swipe actions for dismissing alerts
  - Show alert details with recommended actions
  - _Requirements: 5.2, 5.6_

- [ ] 6.4 Implement insights tab

  - Fetch AI-generated insights from Supabase
  - Display insight cards with confidence levels
  - Filter insights with confidence >= 60%
  - Show different card types (blocker, workload, disengagement, positive)
  - _Requirements: 7.4_

- [ ] 6.5 Implement 1-on-1 prep generation

  - Create bottom sheet for member selection
  - Call generate_1on1_prep RPC function
  - Display talking points, patterns, and blocker history
  - Add loading state during generation (5 second target)
  - _Requirements: 7.5_

- [ ] 7. Build mobile app profile and settings
- [ ] 7.1 Create profile screen

  - Display current streak and longest streak
  - Show role badge (Member/Manager/Admin)
  - Display user avatar and name
  - Add navigation to settings
  - _Requirements: 12.4_

- [ ] 7.2 Implement settings screen

  - Create check-in time preference picker
  - Implement notification toggle switches
  - Add theme preference selector (light/dark)
  - Implement account management options (logout, delete account)
  - _Requirements: 9.5_

- [ ] 8. Implement mobile push notifications
- [ ] 8.1 Set up Expo Push Notifications

  - Configure Expo push notification credentials
  - Request notification permissions on app install
  - Store push tokens in profiles table
  - Handle permission denial gracefully
  - _Requirements: 9.1_

- [ ] 8.2 Implement notification scheduling and delivery

  - Create Supabase Edge Function to send push notifications at check-in time
  - Include streak count in notification for users with 7+ day streaks
  - Implement deep linking to open check-in flow on tap
  - Respect user notification preferences
  - _Requirements: 9.2, 9.3, 9.4, 9.5_

- [ ] 9. Build web dashboard check-in flow
- [ ] 9.1 Create web check-in page

  - Build single-page form with all fields visible
  - Implement WorkloadSelector with large clickable buttons
  - Implement ClaritySelector with large clickable buttons
  - Add FocusInput and BlockerInput text areas
  - _Requirements: 1.2, 1.3, 1.4, 1.5_

- [ ] 9.2 Implement web check-in submission

  - Create React Query mutation for submission
  - Implement auto-save draft to localStorage
  - Add keyboard shortcuts (Tab navigation, Enter to submit)
  - Show celebration modal on successful submission
  - Display real-time character count
  - _Requirements: 1.6, 1.8_

- [ ] 9.3 Add quick picks and validation

  - Implement quick pick suggestions for focus and blockers
  - Add client-side validation (5-500 characters)
  - Display inline error messages
  - Implement progress indicator at top
  - _Requirements: 1.4, 1.5, 1.7_

- [ ] 10. Build web dashboard team health overview
- [ ] 10.1 Create team health metrics display

  - Fetch team health score using calculate_team_health RPC
  - Display health score (0-100) with visual indicator
  - Show active alerts count
  - Display today's check-in completion rate
  - _Requirements: 7.1, 6.2_

- [ ] 10.2 Implement 30-day trend chart

  - Fetch 30 days of team health data
  - Build line chart using Recharts library
  - Show trend direction indicator (up/stable/down)
  - Implement responsive chart sizing
  - _Requirements: 7.1_

- [ ] 10.3 Add real-time updates to dashboard

  - Set up React Query with 30-second refetch interval
  - Subscribe to Supabase Realtime for instant updates
  - Display notification badge for new alerts
  - Implement optimistic UI updates
  - _Requirements: 13.1, 13.2, 13.4_

- [ ] 11. Build web dashboard check-in history
- [ ] 11.1 Create personal check-in history view

  - Fetch user's check-in history from Supabase
  - Display calendar view of past check-ins
  - Show streak visualization
  - Implement date range filter
  - _Requirements: 12.4_

- [ ] 11.2 Implement personal health score trend

  - Calculate personal health score from check-ins
  - Display trend chart for selected date range
  - Show workload and clarity patterns
  - Highlight blocker mentions
  - _Requirements: 7.3_

- [ ] 12. Build web dashboard team members list (managers only)
- [ ] 12.1 Create team members table

  - Fetch all team members with health scores
  - Display table with columns: name, avatar, health score, trend, last check-in, signals
  - Implement sortable columns
  - Add filter by health score range
  - _Requirements: 7.2_

- [ ] 12.2 Implement member detail view

  - Create modal/page for individual member details
  - Display 30-day check-in history
  - Show health score trend chart
  - List active burnout signals
  - _Requirements: 7.3_

- [ ] 12.3 Add hover interactions and tooltips

  - Implement hover state for table rows
  - Show quick preview tooltip on hover
  - Add click handler to navigate to detail view
  - Implement keyboard navigation support
  - _Requirements: 15.2_

- [ ] 13. Build web dashboard insights tab (managers only)
- [ ] 13.1 Create insights card components

  - Build PersistentBlockerCard component
  - Build WorkloadAlertCard component
  - Build DisengagementCard component
  - Build PositivePatternCard component
  - _Requirements: 7.4_

- [ ] 13.2 Implement insights fetching and display

  - Fetch insights from Supabase with confidence >= 60%
  - Display insights sorted by priority and confidence
  - Show confidence level for each insight
  - Display recommended actions
  - _Requirements: 7.4_

- [ ] 13.3 Implement alert dismissal with feedback

  - Add dismiss button to each insight card
  - Create modal for dismissal reason input
  - Store dismissal reason in manager_alerts table
  - Update UI optimistically
  - _Requirements: 5.6_

- [ ] 14. Build web dashboard 1-on-1 prep tool (managers only)
- [ ] 14.1 Create 1-on-1 prep interface

  - Build member selection dropdown
  - Add date range picker (default: last 14 days)
  - Create generate button with loading state
  - Display generated prep in readable format
  - _Requirements: 7.5_

- [ ] 14.2 Implement prep generation

  - Call generate_1on1_prep RPC function with parameters
  - Display summary of recent check-ins
  - Show identified patterns (positive and concerning)
  - List suggested talking points (3-5 bullets)
  - Display relevant blocker history
  - _Requirements: 7.5_

- [ ] 14.3 Add export functionality

  - Implement copy to clipboard button
  - Add export as PDF option
  - Include team health summary in export
  - Format for easy reading
  - _Requirements: 7.6_

- [ ] 15. Implement Custom MCP Server (TeamHealthAnalyzer)
- [ ] 15.1 Set up MCP server project structure

  - Initialize Node.js TypeScript project for MCP server
  - Install dependencies (@modelcontextprotocol/sdk, openai, @supabase/supabase-js)
  - Create folder structure (tools/, services/, utils/)
  - Configure TypeScript and build scripts
  - _Requirements: 2.1, 2.2, 3.1, 4.1, 5.1_

- [ ] 15.2 Implement OpenAI and Supabase clients

  - Create OpenAI client service with API key configuration
  - Create Supabase client service with service role key
  - Implement error handling and retry logic
  - Add logging for debugging
  - _Requirements: 11.1, 2.1_

- [ ] 15.3 Implement detectPersistentBlockers tool

  - Create MCP tool definition for detectPersistentBlockers
  - Fetch blockers for user from Supabase
  - Generate embeddings for new blockers using OpenAI
  - Calculate cosine similarity between blocker embeddings
  - Group similar blockers (similarity > 0.75)
  - Identify persistent blockers (3+ mentions over 14+ days)
  - Calculate severity (high: 5+, medium: 3-4)
  - _Requirements: 2.1, 2.2, 2.3, 2.4, 11.1, 11.2, 11.3_

- [ ] 15.4 Implement analyzeBurnoutRisk tool

  - Create MCP tool definition for analyzeBurnoutRisk
  - Collect all burnout signals (persistent blockers, workload, disengagement)
  - Calculate weighted risk score (0-100)
  - Calculate confidence level based on signal strength
  - Determine if alert should be generated (score >= 50, confidence >= 0.6)
  - _Requirements: 3.1, 3.2, 3.3, 4.1, 4.2, 5.1_

- [ ] 15.5 Implement generateManagerAlert tool

  - Create MCP tool definition for generateManagerAlert
  - Use GPT-4 to generate alert title and description
  - Generate recommended actions based on signals
  - Determine alert priority (high/medium/low)
  - Format alert data for storage in manager_alerts table
  - _Requirements: 5.1, 5.2, 5.3_

- [ ] 15.6 Implement generate1on1Prep tool

  - Create MCP tool definition for generate1on1Prep
  - Fetch check-ins for member in date range
  - Analyze patterns using GPT-4
  - Generate summary of recent check-ins
  - Identify positive and concerning patterns
  - Generate 3-5 talking points
  - Include relevant blocker history
  - _Requirements: 5.5, 7.5_

- [ ] 15.7 Implement utility functions

  - Create cosine similarity calculation function
  - Create health score calculation function
  - Create prompt templates for GPT-4
  - Implement date range utilities
  - _Requirements: 11.2, 7.1_

- [ ]\* 15.8 Write tests for MCP server tools

  - Write unit tests for similarity calculations
  - Write integration tests for each MCP tool with mocked OpenAI/Supabase
  - Test error handling and edge cases
  - Test with sample blocker data
  - _Requirements: All MCP-related requirements_

- [ ] 16. Implement Supabase Edge Functions
- [ ] 16.1 Create send-checkin-reminders Edge Function

  - Write Deno function to fetch users with check-in time = current hour
  - Filter out users who already checked in today
  - Send Slack DM reminders to users
  - Send push notifications to users
  - Schedule function to run hourly using pg_cron
  - _Requirements: 8.1, 9.2_

- [ ] 16.2 Create analyze-checkin Edge Function

  - Write Deno function triggered on check_ins INSERT
  - Update user streak in streaks table
  - Store blocker if mentioned (not "Nothing blocking me")
  - Call MCP server detectPersistentBlockers tool
  - Create burnout_signal if persistent blocker detected
  - Check for overwhelming workload pattern
  - Broadcast team pulse update via Realtime
  - _Requirements: 2.1, 2.5, 3.1, 3.2, 12.1, 12.2, 13.1_

- [ ] 16.3 Create generate-alerts Edge Function

  - Write Deno function scheduled daily at 9am
  - Fetch all teams and team members
  - Call MCP server analyzeBurnoutRisk for each member
  - Generate manager alerts for high-risk members
  - Send Slack DM for high-priority alerts
  - Include medium-priority alerts in daily summary
  - _Requirements: 5.1, 5.3, 5.4_

- [ ] 16.4 Create send-daily-summary Edge Function

  - Write Deno function scheduled daily at 10am
  - Aggregate team check-in data from previous day
  - Post summary to team Slack channel if 8+ check-ins
  - Include medium-priority alerts in summary
  - Post celebration message for 7-day 100% streaks
  - _Requirements: 8.2, 8.4_

- [ ] 17. Implement Slack integration
- [ ] 17.1 Set up Slack app and OAuth

  - Create Slack app with required scopes (chat:write, users:read, im:write)
  - Implement OAuth flow for workspace installation
  - Store access tokens in slack_workspaces table (encrypted)
  - Create API route to handle Slack OAuth callback
  - _Requirements: 8.1, 8.2, 8.3_

- [ ] 17.2 Implement Slack messaging functions

  - Create function to send DM to user by email lookup
  - Create function to post message to team channel
  - Implement message formatting with blocks and attachments
  - Add error handling for Slack API failures
  - _Requirements: 8.1, 8.2, 8.3, 8.4_

- [ ] 17.3 Create Slack reminder messages

  - Design check-in reminder message template
  - Include deep link to mobile app or web dashboard
  - Add streak count for users with 3+ day streaks
  - Implement personalized greeting
  - _Requirements: 8.1, 12.5_

- [ ] 17.4 Create Slack alert messages

  - Design manager alert message template
  - Include team member name, signal type, severity
  - Add recommended actions as bullet points
  - Include link to dashboard for more details
  - _Requirements: 8.3, 5.3_

- [ ] 17.5 Create Slack celebration messages

  - Design team celebration message for 100% check-in rate
  - Include team stats and encouragement
  - Add emoji and formatting for engagement
  - _Requirements: 8.4_

- [ ] 18. Implement Microsoft Teams integration
- [ ] 18.1 Set up Teams bot and OAuth

  - Register Teams bot in Azure Bot Service
  - Configure OAuth 2.0 with Microsoft identity platform
  - Request required permissions (Chat.ReadWrite, ChannelMessage.Send, User.Read)
  - Store access tokens and refresh tokens in teams_workspaces table (encrypted)
  - Create API route to handle Teams OAuth callback
  - _Requirements: 17.5, 17.1_

- [ ] 18.2 Implement Teams Bot Framework integration

  - Install Bot Framework SDK for Node.js
  - Create bot adapter and conversation handler
  - Implement message routing for bot commands
  - Handle token refresh logic for expired tokens
  - Add error handling for Teams API failures
  - _Requirements: 17.9, 17.10_

- [ ] 18.3 Implement Teams messaging functions

  - Create function to send 1:1 chat message to user by email lookup
  - Create function to post message to team channel
  - Implement Adaptive Cards for rich message formatting
  - Add deep linking to web dashboard
  - _Requirements: 17.1, 17.2, 17.6, 17.7_

- [ ] 18.4 Create Teams reminder messages

  - Design check-in reminder Adaptive Card template
  - Include action button linking to check-in flow
  - Add streak count for users with 3+ day streaks
  - Implement personalized greeting
  - _Requirements: 17.1, 17.6_

- [ ] 18.5 Create Teams alert messages

  - Design manager alert Adaptive Card template
  - Include team member name, signal type, severity
  - Add recommended actions as bullet points
  - Include action button linking to alert detail page
  - _Requirements: 17.3, 17.7_

- [ ] 18.6 Create Teams celebration messages

  - Design team celebration Adaptive Card for 100% check-in rate
  - Include team stats and encouragement
  - Add celebratory visuals and formatting
  - _Requirements: 17.4_

- [ ] 18.7 Implement Teams bot commands

  - Implement "/checkin" command to open check-in flow
  - Implement "/status" command to show personal stats
  - Implement "/team" command to show team pulse
  - Add help command with available commands list
  - _Requirements: 17.9_

- [ ] 18.8 Create integration preference selector

  - Build UI for managers to choose Slack, Teams, or both
  - Store preference in integration_preferences table
  - Update notification logic to respect preference
  - Handle fallback to email when integration disconnected
  - _Requirements: 17.10_

- [ ] 18.9 Update Edge Functions to support Teams

  - Modify send-checkin-reminders to send Teams messages
  - Modify generate-alerts to send Teams messages
  - Modify send-daily-summary to post to Teams channel
  - Add logic to check integration preference
  - _Requirements: 17.1, 17.2, 17.3, 17.4, 17.8_

- [ ] 18.10 Implement Teams report delivery

  - Send weekly technical reports via Teams chat
  - Send monthly reports via Teams chat
  - Format reports as Adaptive Cards with download links
  - _Requirements: 17.8_

- [ ] 19. Implement shared types and utilities
- [ ] 19.1 Create shared types package

  - Define TypeScript interfaces for all data models
  - Export types for Profile, Team, CheckIn, Blocker, etc.
  - Create enums for workload, clarity, role, priority, severity
  - Set up package to be imported by mobile, web, and MCP server
  - _Requirements: All requirements_

- [ ] 19.2 Create shared validation utilities

  - Implement check-in field validation (character limits)
  - Create email validation function
  - Implement date range validation
  - Create health score calculation utility
  - _Requirements: 1.4, 1.5, 7.1_

- [ ] 19.3 Create shared constants

  - Define color palette constants
  - Define typography scale
  - Define spacing scale
  - Define API endpoints
  - _Requirements: Design system_

- [ ] 20. Implement accessibility features
- [ ] 20.1 Add accessibility to mobile app

  - Add accessibility labels to all interactive elements
  - Implement screen reader support with proper announcements
  - Test with iOS VoiceOver and Android TalkBack
  - Ensure minimum touch target size (44x44)
  - _Requirements: 15.1_

- [ ] 20.2 Add accessibility to web dashboard

  - Implement keyboard navigation with visible focus indicators
  - Add ARIA labels to all interactive elements
  - Ensure color contrast ratios (4.5:1 for normal, 3:1 for large)
  - Test with screen readers (NVDA, JAWS)
  - Implement skip navigation links
  - _Requirements: 15.1, 15.2, 15.3_

- [ ] 20.3 Implement reduced motion support

  - Detect prefers-reduced-motion preference
  - Disable animations when preference is set
  - Provide instant transitions as alternative
  - Test on both mobile and web
  - _Requirements: 15.4_

- [ ] 20.4 Implement accessible error handling

  - Announce errors to screen readers
  - Provide clear, actionable error messages
  - Display field-level errors with proper associations
  - Ensure error states have sufficient color contrast
  - _Requirements: 15.5_

- [ ] 21. Implement data encryption and security
- [ ] 21.1 Set up encryption for sensitive data

  - Configure AES-256 encryption for check-in text fields
  - Encrypt Slack and Teams access tokens before storage
  - Implement encryption utilities for mobile and web
  - Set up key management system
  - _Requirements: 14.1_

- [ ] 21.2 Implement TLS for all network requests

  - Configure Supabase client to use TLS 1.3
  - Verify all API routes use HTTPS
  - Implement certificate pinning for mobile app
  - _Requirements: 14.5_

- [ ] 21.3 Implement data anonymization

  - Create function to anonymize check-in data
  - Implement background job for account deletion
  - Test anonymization preserves aggregate metrics
  - Verify anonymized data cannot be re-identified
  - _Requirements: 10.5, 14.4_

- [ ] 22. Polish and optimization
- [ ] 22.1 Optimize mobile app performance

  - Implement code splitting and lazy loading
  - Optimize images and assets
  - Minimize bundle size
  - Test app launch time (target: < 2 seconds)
  - _Requirements: Performance testing_

- [ ] 22.2 Optimize web dashboard performance

  - Implement Next.js image optimization
  - Add route-based code splitting
  - Optimize bundle size with tree shaking
  - Run Lighthouse CI and achieve 90+ scores
  - _Requirements: Performance testing_

- [ ] 22.3 Implement error monitoring

  - Set up Sentry for error tracking (mobile and web)
  - Add custom error boundaries
  - Implement logging for MCP server
  - Set up alerts for critical errors
  - _Requirements: Error handling_

- [ ] 22.4 Add loading states and skeletons

  - Create skeleton screens for all major views
  - Implement loading spinners for async operations
  - Add progress indicators for multi-step flows
  - Ensure loading states are accessible
  - _Requirements: User experience_

- [ ] 23. Testing and quality assurance
- [ ] 23.1 Set up testing infrastructure

  - Configure Jest for unit tests
  - Set up React Native Testing Library for mobile
  - Set up React Testing Library for web
  - Configure Detox for mobile E2E tests
  - Configure Playwright for web E2E tests
  - _Requirements: Testing strategy_

- [ ]\* 23.2 Write unit tests for mobile app

  - Test check-in flow components
  - Test team pulse visualization
  - Test manager dashboard components
  - Test utility functions and hooks
  - Achieve 70%+ code coverage
  - _Requirements: Testing strategy_

- [ ]\* 23.3 Write unit tests for web dashboard

  - Test check-in form components
  - Test team health overview
  - Test insights and alerts components
  - Test API routes
  - Achieve 70%+ code coverage
  - _Requirements: Testing strategy_

- [ ]\* 23.4 Write integration tests

  - Test check-in submission flow (mobile and web)
  - Test real-time updates
  - Test offline mode and sync
  - Test alert generation flow
  - _Requirements: Testing strategy_

- [ ]\* 23.5 Write E2E tests for critical paths

  - Test new user onboarding to first check-in
  - Test manager viewing dashboard and generating 1-on-1 prep
  - Test persistent blocker detection and alert flow
  - Run tests on CI/CD pipeline
  - _Requirements: Testing strategy_

- [ ] 24. Deployment and launch preparation
- [ ] 24.1 Deploy backend services

  - Deploy Supabase project to production
  - Deploy MCP server to cloud provider (AWS Lambda or containerized)
  - Configure environment variables for production
  - Set up database backups and monitoring
  - _Requirements: Deployment architecture_

- [ ] 24.2 Deploy web dashboard

  - Deploy Next.js app to Vercel
  - Configure custom domain and SSL
  - Set up environment variables
  - Configure CDN and edge functions
  - _Requirements: Deployment architecture_

- [ ] 24.3 Build and submit mobile apps

  - Build iOS app with EAS Build
  - Build Android app with EAS Build
  - Submit iOS app to TestFlight for beta testing
  - Submit Android app to Google Play internal testing
  - Configure OTA updates with EAS Update
  - _Requirements: Deployment architecture_

- [ ] 24.4 Set up monitoring and analytics

  - Configure Supabase analytics
  - Set up application performance monitoring
  - Implement user analytics (privacy-focused)
  - Set up uptime monitoring
  - _Requirements: Deployment architecture_

- [ ] 25. Implement team progress reporting system
- [ ] 25.1 Create tasks table and API

  - Add tasks table to database schema for work item tracking
  - Create API routes for CRUD operations on tasks
  - Implement task status updates (todo, in_progress, done, blocked)
  - Add story points and category fields
  - _Requirements: 16.1, 16.2, 16.3_

- [ ] 25.2 Create team_reports table and storage

  - Add team_reports table to database schema
  - Set up Supabase Storage bucket for report PDFs
  - Create indexes for efficient report querying
  - Implement RLS policies (managers only)
  - _Requirements: 16.4, 16.5_

- [ ] 25.3 Implement generateTeamReport MCP tool

  - Create MCP tool definition for generateTeamReport
  - Implement data aggregation for weekly technical reports
  - Implement data aggregation for monthly technical reports
  - Implement data aggregation for monthly business reports
  - Use GPT-4 to generate summaries and insights in appropriate language
  - _Requirements: 16.1, 16.2, 16.3, 16.6, 16.7_

- [ ] 25.4 Build report generation logic for weekly technical reports

  - Fetch tasks completed in date range
  - Aggregate blockers encountered and their resolution status
  - Calculate team velocity metrics (check-ins per day, completion rate)
  - Identify active technical debt items
  - Format data for technical audience
  - _Requirements: 16.1, 16.7_

- [ ] 25.5 Build report generation logic for monthly technical reports

  - Aggregate task breakdown by category (features, bugs, debt, other)
  - Extract technical achievements from check-ins and completed tasks
  - Identify technical challenges from persistent blockers
  - Calculate team health trend (start vs end score)
  - Aggregate burnout signals (detected, resolved, active)
  - Calculate technical debt metrics (added vs resolved)
  - _Requirements: 16.3, 16.7_

- [ ] 25.6 Build report generation logic for monthly business reports

  - Generate executive summary using GPT-4
  - Extract features delivered with plain-language descriptions
  - Calculate total story points completed
  - Calculate team health score trend
  - Identify key achievements in business terms
  - Extract risks and concerns with severity levels
  - Generate recommendations for next month
  - _Requirements: 16.2, 16.6_

- [ ] 25.7 Create web dashboard reports page

  - Build reports page with report type selector
  - Add date range picker with smart defaults
  - Implement generate report button with loading state
  - Display generated report in readable format
  - Add export buttons (PDF, Markdown)
  - _Requirements: 16.4_

- [ ] 25.8 Implement report export functionality

  - Create PDF export using a library (e.g., jsPDF or Puppeteer)
  - Implement Markdown export with proper formatting
  - Store exported reports in Supabase Storage
  - Generate shareable links for reports
  - _Requirements: 16.4_

- [ ] 25.9 Implement scheduled report generation

  - Create Supabase Edge Function for weekly report generation
  - Schedule function to run every Friday at 5pm using pg_cron
  - Create Supabase Edge Function for monthly report generation
  - Schedule function to run on last day of month using pg_cron
  - Send reports via email, Slack, and Teams to managers
  - _Requirements: 16.8, 16.9, 17.8_

- [ ] 25.10 Add report history view

  - Create page to view previously generated reports
  - Display reports in chronological order with filters
  - Allow downloading of historical reports
  - Show report metadata (type, date range, generated by)
  - _Requirements: 16.5_

- [ ] 25.11 Implement task management interface (optional)

  - Create simple task board for managers to track work items
  - Add ability to create, update, and complete tasks
  - Implement story point assignment
  - Add task categorization (feature, bug, technical debt)
  - Link tasks to team members
  - _Requirements: 16.1, 16.2, 16.3_

- [ ]\* 25.12 Write tests for reporting system
  - Test report generation for all three report types
  - Test data aggregation logic
  - Test PDF and Markdown export
  - Test scheduled report generation
  - _Requirements: 16.1, 16.2, 16.3_
