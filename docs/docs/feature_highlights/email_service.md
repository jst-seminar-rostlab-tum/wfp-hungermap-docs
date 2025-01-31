import ReactPlayer from 'react-player'

# Email Service (Newsletter)

Author: `Shaurya Sharma`

The **Email Service** (also referred to as the Newsletter) provides an automated, customizable mechanism for delivering email communications within the HungerMap platform. It leverages [Brevo](https://www.brevo.com/) for reliable email delivery and includes subscription management, daily report distribution, and an **Admin Mode** feature for extended functionality.

<ReactPlayer 
  url="/videos/email_service.mp4" 
  controls={true}
  width="100%"
  height="auto"
/>


### Core Functions

1. **Sending Formatted Emails**  
   - Leverages HTML CSS templates
   - Dynamic content injection using template variables  
   - Supports sending to one or multiple recipients simultaneously  

2. **Subscription Management**  
   - Create and manage **topics**, which are categories of email content  
   - Subscribe or unsubscribe endpoints to handle user preferences 

3. **Admin Mode & Role Management**  
   - **Admin Mode**: A permission level that unlocks additional capabilities (admin topics) 
   - Ability to assign different **roles** (e.g., _RestrictedUser_, _admin_ etc.) to specific topics  
   - Role-based access to restricted topics


### Conclusion

The Email Service (Newsletter) is an essential part of the HungerMap, offering both standard and advanced tools to keep users informed. Its topic-based subscription system, template handling, and automated distribution of daily reports streamline communication, while Admin Mode adds an extra level of control and customization.

By assigning roles to topics and selectively granting admin privileges, it can effectively manage large-scale email campaigns and maintain a secure, well-structured outreach strategy. From everyday subscribers interested in daily reports to admins overseeing sensitive or internal announcements, the Email Service ensures that the right information reaches the right people—efficiently and reliably.