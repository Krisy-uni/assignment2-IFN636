# Calendar-based Work Scheduling Web Application

JobFlow is a mobile-first web application designed to support job scheduling, worker allocation, timesheet approval and payslip management for small trade businesses.

## Deployment （either link is okay）

https://test-aunk.onrender.com/login/login.html

## Demo Login Accounts

The following demo accounts can be used to test Owner and Worker access.

### Owner Account

- **Email:** `owner@jobflow.com`
- **Password:** `owner123`
- **Redirect:** `/owner-view/owner-view.html`

### Worker Accounts

| Worker | Email | Password |
|---|---|---|
| Sarah Johnson | `sarah@jobflow.com` | `Worker123` |
| Mike Lee | `mike@jobflow.com` | `Worker123` |
| Emma Brown | `emma@jobflow.com` | `Worker123` |
| James Wilson | `james@jobflow.com` | `Worker123` |

Worker accounts are redirected to `/worker-view/worker-view.html` after successful login.

These accounts are provided for demonstration and testing only.



## Main Features

### Owner Features

#### Features Completed in Assignment 1

##### 1. Calendar and Job Scheduling

- View scheduled jobs in a two-week calendar.
- Select a date to view its jobs.
- Identify Pending, Accepted, Declined and Finished jobs through status indicators.
- Create jobs with task details, scheduled times, location and client information.

##### 2. Worker Allocation

- Assign existing Workers to jobs.
- View Workers' responses to job offers.
- Reassign jobs when required.

#### Features Added After Assignment 1

##### 3. Worker Account Management

- Add Worker accounts with a name, email, phone number and temporary password.
- Activate or deactivate Worker accounts.
- Configure Worker hourly rates.

##### 4. Timesheet Review and Approval

- Review submitted jobs, completion notes and photos.
- Review recorded clock-in and clock-out times.
- Select Actual, Scheduled or Manual values for each time entry.
- Approve working hours and record the hourly rate used for payroll.
- Undo approval to return a timesheet to pending approval.

##### 5. Payroll and Payslips

- Review approved weekly hours and payroll estimates.
- View gross pay, tax estimates, superannuation, net pay and YTD totals.
- Generate and push payslips to Workers through the application.
- Save pay-period details in MongoDB when payslips are pushed.
- Preserve released payslip details when job records or hourly rates change.

##### 6. Profile and Account

- Update the signed-in Owner's profile details.
- Change the account password by entering the current password and a new password.

### Worker Features

#### Features Completed in Assignment 1

##### 1. Calendar and Assigned Jobs

- View assigned jobs in a two-week calendar.
- Select a date to view its assigned jobs.
- View task details, scheduled times, location and client contact information.

##### 2. Job Offer Responses

- Accept pending job offers.
- Decline pending job offers.

##### 3. Time Recording and Job Completion

- Clock in and out on accepted jobs.
- Submit completed jobs.
- Add optional completion notes.
- Upload optional completion photos.

#### Features Added After Assignment 1

##### 4. Timesheet Approval Information

- View whether a completed job is awaiting approval or has been approved.
- View approved working-time information for completed jobs.

##### 5. Payslips

- View payslips released by the Owner.
- Review pay-period details, daily hours and YTD totals.
- Download payslips as PDFs generated from saved pay-period records.
- Access only the signed-in Worker's own payslips.

##### 6. Profile and Account

- Update the signed-in Worker's profile details.
- Change the account password by entering the current password and a new password.

### Improvements to Existing Features

#### 1. Persistent Photo Storage

- Store completion photos in MongoDB GridFS.
- Keep photos available after application redeployment.
- Check access before serving photos to an Owner or the assigned Worker.

#### 2. Reliable Completion Submission

- Prevent repeated submissions from overwriting completed-job information.
- Attempt to clean up uploaded photos when submission fails.

#### 3. Shared Interface Styling

- Use a shared global stylesheet for common interface rules.
- Apply consistent alignment to calendar dates and avatar initials across pages.

## 3 ways to Setup

###  Way 1: Deploy on Render

1. Push the project to GitHub.

2. Sign in to Render and select **New → Web Service**.

3. Connect GitHub and select the project repository.

4. Configure the service:

   | Setting | Value |
   |---|---|
   | Runtime | Node |
   | Branch | main |
   | Root Directory | Leave blank |
   | Build Command | npm install |
   | Start Command | node server.js |

5. Add these environment variables:

   | Key | Value |
   |---|---|
   | MONGODB_URI | Your MongoDB Atlas connection string |
   | SESSION_SECRET | A long, private random secret |
   | NODE_ENV | production |

6. Add the service's outbound IP addresses from **Render → Connect → Outbound** to the MongoDB Atlas **Network Access** list.

7. Select **Deploy Web Service** and wait for deployment to complete.

8. Open the Render URL followed by `/login/login.html`.

#####  Live Application

https://test-aunk.onrender.com/login/login.html

### Way 2: Deploy on AWS EC2

1. Launch an Ubuntu EC2 instance with a public IP address and an SSH key pair.

2. Configure its security group:

   | Port | Purpose | Source |
   |---|---|---|
   | 22 | SSH | Your IP address |
   | 3000 | Application | Allow visitors to the demo |

3. Connect to the instance using SSH:

   ```bash
   chmod 400 your-key.pem
   ssh -i your-key.pem ubuntu@your-ec2-public-ip
   ```

4. Install Git and a supported Node.js LTS version, including npm.

5. Clone the repository and install dependencies:

   ```bash
   git clone https://github.com/Krisy-uni/test.git
   cd test
   npm install
   ```

6. Create a `.env` file in the project root:

   ```env
   MONGODB_URI=your-mongodb-connection-string
   SESSION_SECRET=your-private-random-session-secret
   NODE_ENV=development
   ```

   This configuration is for an HTTP demonstration. For production,
   configure HTTPS and use `NODE_ENV=production`.

7. Add the EC2 public IP address to MongoDB Atlas's
   **Network Access** list.

8. Start the application:

   ```bash
   node server.js
   ```

9. Open:

   ```text
   http://your-ec2-public-ip:3000/login/login.html
   ```

The server runs while the terminal process remains active.
For continuous hosting, configure a process manager or system service.

Photos and released payslip details remain in MongoDB.
No local persistent storage is required for these features.

### Way 3: Local Setup

1. Clone the repository and open the project folder:

   ```bash
   git clone https://github.com/Krisy-uni/test.git
   cd test
   ```

2. Install dependencies:

   ```bash
   npm install
   ```

3. Create a `.env` file in the project root:

   ```env
   MONGODB_URI=your-mongodb-connection-string
   SESSION_SECRET=your-private-random-session-secret
   NODE_ENV=development
   ```

4. In MongoDB Atlas, allow your computer's IP address under **Network Access**. Use the database containing the demo accounts.

5. Start the server:

   ```bash
   node server.js
   ```

6. Open http://localhost:3000/login/login.html and sign in using a demo account.

Node.js and npm must be installed. Do not commit your `.env` file to GitHub.



# System Overview

## Job Scheduling and Allocation

Owners create jobs by entering the task, date, start and end times,
location and client contact details. They then assign a Worker to
each job.

Workers can accept or decline job offers. Owners can reassign a job
to another Worker when needed.

Both roles use the calendar to view jobs by date. Coloured indicators
show whether jobs are Pending, Accepted, Declined or Finished.

## Worker Management

Owners can add Workers, activate or deactivate their accounts,
and set their hourly rates.

To add a Worker, the Owner enters their name, email address, phone
number and a temporary password.

## Job Completion and Timesheet Approval

Workers can clock in and out, add a completion note, upload photos
and submit a completed job. Clock records, notes and photos are optional.

Workers can upload up to 10 JPG, PNG or WebP photos, with a maximum
size of 5 MB each.

After submission, the job is marked Finished and awaits the Owner's
approval of its working hours.

The Owner reviews the job and chooses the start and end times using:

- Actual clock records.
- Scheduled job times.
- Manually entered times.

The Owner can undo an approval if a correction is needed. This does
not change a payslip that has already been released.

## Completed Job Details and Photos

Owners and assigned Workers can view completed job details, including
working times, completion notes, photos and approval information.

Photos are saved in MongoDB rather than on the hosting server.
They remain available after the application is redeployed.

## Payroll and Payslips

Owners review approved weekly hours and select Generate Payslips,
then Confirm & Push.

Each Worker's payslip details are saved in MongoDB when pushed.
Workers can then view their payslips and download them as PDFs.

Released payslips keep the details saved at the time of pushing.
Changing a Worker's hours or hourly rate later does not change
an existing payslip.

Each Worker can receive one payslip per week. Pushing the same week
again does not replace it. Payslips are shared through the app,
not by email.

### Payroll Calculations

Pay is calculated from approved working hours and hourly rates.

The payslip shows gross pay, estimated tax, superannuation, net pay
and year-to-date (YTD) totals. YTD covers the financial year starting
on 1 July.

Superannuation is calculated at 12%. Tax is an estimate and does not
include the Medicare levy, offsets or other deductions.

## My Profile

Owners and Workers can update their profile details and change their
password while signed in.

Email-based password recovery is not currently available.

## How Data Is Stored

The website uses an Express backend to save and retrieve data from
MongoDB.

MongoDB stores accounts, jobs, completion photos and released
payslip details. When a Worker downloads a payslip, the backend
creates a PDF from the saved details.

Users may need to sign in again after the server restarts.

## Current Limitations

- Forgotten passwords cannot currently be reset by email.
- Released payslips cannot be corrected or replaced through the app.
- Changes to older job records may affect YTD totals in future
  payslips. Already released payslips remain unchanged.
- Payroll figures are estimates.
- Photos lost before the storage update need to be uploaded again.

## Before Real Business Use

The application is a prototype. Before using it for real business
operations, it needs further work on login-session storage,
account security, backups and payroll accuracy.
