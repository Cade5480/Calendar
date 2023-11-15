## 1. The Student Calendar
For this project I decided to create a Calendar to help students plan their day, week, or month. As students it can be hard to keep a schedule for things. 
This can involve scenarios like saying you will do homework, but never doing it, studying at a certain time, or just to give yourself simple reminders about upcoming due dates.
The idea behind this calendar is to put those tasks into a visualization so you can't ever forget to do one of these things.

## 2.  Steps To Creating Student Calendar
	- Login and Registration
	- The Calendar Form
	- The Event Form

  # 1. Login Form
  The login form will be the first form encountered when running this project. The form was created to replicate a user login for students who would like access to the student calendar.
  This form is connected to an SQL database to check to see if the entered username and password exists and grants the login. The following code shows the logic behind this form. If the user 
  enters the username and password it checks the database to see if it exists. If it doesn't, it will say account does not exist. You will then need to create an account.
  
      private void button1_Click(object sender, EventArgs e)
        {
            if (txtpassword.Text != string.Empty || txtusername.Text != string.Empty)
            {

                cmd = new SqlCommand("select * from LoginTable where username='" + txtusername.Text + "' and password='" + txtpassword.Text + "'", cn);
                dr = cmd.ExecuteReader();
                if (dr.Read())
                {
                    dr.Close();
                    this.Hide();
                    Calendar calendar = new Calendar();
                    calendar.ShowDialog();
                }
                else
                {
                    dr.Close();
                    MessageBox.Show("No Account avilable with this username and password ", "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
                }

            }
            else
            {
                MessageBox.Show("Please enter value in all field.", "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
            }
        }
# Stored Procedures for Login Form Include:
Selecting the user by username to check to see if the username is in the database.

    CREATE PROCEDURE SelectUserAndPass
        @username NVARCHAR(MAX),
        @password NVARCHAR(MAX)
    AS
    BEGIN
        SELECT *
        FROM LoginTable
        WHERE username = @username AND password = @password
    END

   # 2. Registration Form
   After clicking the Sign Up button on the Login Form, the form will hide and bring up the Registration Form.

         private void button2_Click(object sender, EventArgs e)
        {
            this.Hide();
            Registration registration = new Registration();
            registration.ShowDialog();
        }
The Registration form allows you to create an account to gain access to the Calendar to store events on certain dates.
It accesses the database to check if the entered username and password already exists. If either or exists it will notify you of so.
When you enter a valid username and password, it will be stored inside of the database and saved for next time, when you login.

     private void Registration_Load(object sender, EventArgs e)
        {
            cn = new SqlConnection(@"Data Source=DESKTOP-0FLSOFJ\SQLEXPRESS;Initial Catalog=Calendar;Integrated Security=True");
            cn.Open();
        }

        private void RegisterButton_Click(object sender, EventArgs e)
        {
            if (txtconfirmpassword.Text != string.Empty || txtpassword.Text != string.Empty || txtusername.Text != string.Empty)
            {
                if (txtpassword.Text == txtconfirmpassword.Text)
                {
                    cmd = new SqlCommand("select * from LoginTable where username='" + txtusername.Text + "'", cn);
                    dr = cmd.ExecuteReader();
                    if (dr.Read())
                    {
                        dr.Close();
                        MessageBox.Show("Username Already exist please try another ", "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
                    }
                    else
                    {
                        dr.Close();
                        cmd = new SqlCommand("insert into LoginTable values(@username,@password)", cn);
                        cmd.Parameters.AddWithValue("username", txtusername.Text);
                        cmd.Parameters.AddWithValue("password", txtpassword.Text);
                        cmd.ExecuteNonQuery();
                        MessageBox.Show("Your Account is created . Please login now.", "Done", MessageBoxButtons.OK, MessageBoxIcon.Information);
                    }
                }
                else
                {
                    MessageBox.Show("Please enter both password same ", "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
                }
            }
            else
            {
                MessageBox.Show("Please enter value in all field.", "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
            }
        }

 # Stored Procedures for Registration Form Include:
1. Selecting the user by username to check to see if the username is in the database.

        CREATE PROCEDURE SelectUserByUsername
            @username NVARCHAR(MAX)
        AS
        BEGIN
            SELECT *
            FROM LoginTable
            WHERE username = @username
        END
2. Inserts the entered username and password into the database

        CREATE PROCEDURE InsertUser
            @username NVARCHAR(MAX),
            @password NVARCHAR(MAX)
        AS
        BEGIN
            INSERT INTO LoginTable (username, password)
            VALUES (@username, @password)
        END


# 3. Calendar Form
The Calendar Form is a simple form that is comprised of user control boxes that allow for interaction. The month and year is presented at the top of the form alongside the days of the week.
When you observe the form, you will notice the days of the month are correctly incremented and will continue to be when hitting next or previous month.
The Calendar Form is interactable like mentioned before, you are able to click on a day of the month and add a new event to the calendar for your future self.

    private void Prevbtn_Click(object sender, EventArgs e)
        {
            dayContainer.Controls.Clear();                            //clears container
            month--;                                                  //decrements month to go to next month

            String monthname = DateTimeFormatInfo.CurrentInfo.GetMonthName(month);
            LBDATE.Text = monthname + " " + year;

            static_month = month;
            static_year = year;

            DateTime now = DateTime.Now;
            DateTime startofthemonth = new DateTime(year, month, 1);  //gets first day of the month

            int days = DateTime.DaysInMonth(year, month);            //gets the count of days of the month

            int dayoftheweek = Convert.ToInt32(startofthemonth.DayOfWeek.ToString("d")) + 1;   //converts startofthemonth to integer

            //user control
            for (int i = 1; i < dayoftheweek; i++)
            {
                UserControlBlank ucBlank = new UserControlBlank();
                dayContainer.Controls.Add(ucBlank);
            }

            //create user control for days
            for (int i = 1; i <= days; i++)
            {
                UserControlDays ucdays = new UserControlDays();
                ucdays.days(i);
                dayContainer.Controls.Add(ucdays);
            }
        }

        private void button2_Click(object sender, EventArgs e)
        {

            dayContainer.Controls.Clear();                            //clears container
            month++;                                                  //increments month to go to next month

            String monthname = DateTimeFormatInfo.CurrentInfo.GetMonthName(month);
            LBDATE.Text = monthname + " " + year;

            static_month = month;
            static_year = year;

            DateTime now = DateTime.Now;
            DateTime startofthemonth = new DateTime(year, month, 1);  //gets first day of the month

            int days = DateTime.DaysInMonth(year, month);            //gets the count of days of the month

            int dayoftheweek = Convert.ToInt32(startofthemonth.DayOfWeek.ToString("d")) + 1;   //converts startofthemonth to integer

            //user control
            for (int i = 1; i < dayoftheweek; i++)
            {
                UserControlBlank ucBlank = new UserControlBlank();
                dayContainer.Controls.Add(ucBlank);
            }

            //create user control for days
            for (int i = 1; i <= days; i++)
            {
                UserControlDays ucdays = new UserControlDays();
                ucdays.days(i);
                dayContainer.Controls.Add(ucdays);
            }
        }

# 4. The Event Form
The final form of this project is the Event Form. When clicking on a day of the month you will be brough to the event form. It will have the date autofilled
and a textbox for you to enter in any event of your choosing, whether it be when you need to do homework, study, or anything really. This event will be saved in the database 
and be displayed on the calendar for your viewing.

    public EventForm()
        {
            InitializeComponent();
        }

        private void EventForm_Load_1(object sender, EventArgs e)
        {
            cn = new SqlConnection(@"Data Source=DESKTOP-0FLSOFJ\SQLEXPRESS;Initial Catalog=Calendar;Integrated Security=True");
            cn.Open();
            txdate.Text = Calendar.static_month + "/" + UserControlDays.static_day + "/" + Calendar.static_year;
        }

        private void label1_Click(object sender, EventArgs e)
        {


        }

        private void txdate_TextChanged(object sender, EventArgs e)
        {

        }

        private void txevent_TextChanged(object sender, EventArgs e)
        {

        }

        private void btnSave_Click(object sender, EventArgs e)
        {

                cmd = new SqlCommand("INSERT INTO EventTable VALUES(@date,@event)", cn);
                cmd.Parameters.AddWithValue("date", txdate.Text);
                cmd.Parameters.AddWithValue("event", txevent.Text);
                cmd.ExecuteNonQuery();

                MessageBox.Show("Saved");
                cn.Close();
                this.Close();
            }
        }
    }
# Stored Procedure For Event Form
1. Inserts new date and event into the database when entered

        CREATE PROCEDURE InsertEvent
            @date DATE,
            @event NVARCHAR(MAX)
        AS
        BEGIN
            INSERT INTO EventTable (date, event)
            VALUES (@date, @event)
        END
# ---------------------------------------------------------------
# Thank You For Your Time!
# ---------------------------------------------------------------
