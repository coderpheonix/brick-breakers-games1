# brick-breakers-games1
This is my final project for Sandford university code in place 2024




# This is my final project for standford university code in place program 2024


import tkinter as tk  # Import the tkinter library for GUI
import random  # Import the random library to generate random colors for bricks

# Constants for game setup
CANVAS_WIDTH = 800  # Width of the game canvas
CANVAS_HEIGHT = 600  # Height of the game canvas
BRICK_WIDTH = 75  # Width of each brick
BRICK_HEIGHT = 20  # Height of each brick
BRICK_ROWS = 5  # Number of rows of bricks
BRICK_COLS = 10  # Number of columns of bricks
PADDLE_WIDTH = 100  # Width of the paddle
PADDLE_HEIGHT = 10  # Height of the paddle
BALL_SIZE = 20  # Diameter of the ball
BALL_SPEED = 10  # Speed of the ball


# Class for the Brick Breaker game
class BrickBreaker:
    def __init__(self, root): # we can call this function in main function
        self.canvas = tk.Canvas(root, width=CANVAS_WIDTH, height=CANVAS_HEIGHT,
                                bg='white')  # Create a canvas for the game
        self.canvas.pack()  # Add the canvas to the window
        self.paddle = self.canvas.create_rectangle(350, 550, 450, 560, fill='blue')  # Create the paddle
        self.ball = self.canvas.create_oval(390, 540, 410, 560, fill='red')  # Create the ball
        self.ball_dx = BALL_SPEED  # Initial horizontal speed of the ball
        self.ball_dy = -BALL_SPEED  # Initial vertical speed of the ball
        self.misses = 0  # Counter for missed balls
        self.game_over = False  # Flag to indicate if the game is over
        self.bricks = []  # List to store the bricks
        for i in range(BRICK_ROWS):  # Loop to create bricks row by row
            for j in range(BRICK_COLS):  # Loop to create bricks column by column
                x1 = j * (BRICK_WIDTH + 5)  # X-coordinate of the top-left corner of the brick
                y1 = i * (BRICK_HEIGHT + 5)  # Y-coordinate of the top-left corner of the brick
                x2 = x1 + BRICK_WIDTH  # X-coordinate of the bottom-right corner of the brick
                y2 = y1 + BRICK_HEIGHT  # Y-coordinate of the bottom-right corner of the brick
                brick = self.canvas.create_rectangle(x1, y1, x2, y2, fill=random.choice(
                    ['red', 'blue', 'green', 'yellow']))  # Create the brick with a random color
                self.bricks.append(brick)  # Add the brick to the list of bricks
        self.canvas.bind('<Motion>', self.move_paddle)  # Bind the mouse motion to the paddle movement
        self.game_loop()  # Start the game loop

    def move_paddle(self, event): # creating function for paddal
        paddle_x = event.x - PADDLE_WIDTH / 2  # Calculate the new x-coordinate of the paddle
        self.canvas.coords(self.paddle, paddle_x, 550, paddle_x + PADDLE_WIDTH, 560)  # Update the paddle's position

    def game_loop(self): # class and loop this loop will continue as soon as the bricks ends
        if not self.game_over:  # Continue the game loop only if the game is not over
            self.move_ball()  # Move the ball
            self.check_collisions()  # Check for collisions
            self.canvas.after(20, self.game_loop)  # Call game_loop again after 20 milliseconds to create a game loop

    def move_ball(self): # creating this function for movement ball and its all features like clour speed
        self.canvas.move(self.ball, self.ball_dx, self.ball_dy)  # Move the ball by its speed
        ball_pos = self.canvas.coords(self.ball)  # Get the current position of the ball
        if ball_pos[0] <= 0 or ball_pos[2] >= CANVAS_WIDTH:  # Check if the ball hits the left or right wall
            self.ball_dx = -self.ball_dx  # Reverse the horizontal direction of the ball
        if ball_pos[1] <= 0:  # Check if the ball hits the top wall
            self.ball_dy = -self.ball_dy  # Reverse the vertical direction of the ball
        if ball_pos[3] >= CANVAS_HEIGHT:  # Check if the ball hits the bottom wall
            self.misses += 1  # Increment the miss counter
            if self.misses > 3:  # Check if the miss counter exceeds three
                self.game_over = True  # Set the game over flag to True
                self.canvas.create_text(CANVAS_WIDTH / 2, CANVAS_HEIGHT / 2,
                                        text="Game Over", font=('Helvetica', 36, 'bold'), fill='red')  # Display "Game Over" message
            else:
                self.canvas.coords(self.ball, 390, 540, 410, 560)  # Reset the ball to the starting position
                self.ball_dy = -BALL_SPEED  # Reset the vertical speed of the ball

    def check_collisions(self):
        ball_pos = self.canvas.coords(self.ball)  # Get the current position of the ball
        paddle_pos = self.canvas.coords(self.paddle)  # Get the current position of the paddle
        if ball_pos[2] >= paddle_pos[0] and ball_pos[0] <= paddle_pos[2] and ball_pos[3] >= paddle_pos[
            1]:  # Check if the ball hits the paddle
            self.ball_dy = -self.ball_dy  # Reverse the vertical direction of the ball

        for brick in self.bricks:  # Loop through each brick
            brick_pos = self.canvas.coords(brick)  # Get the current position of the brick
            if ball_pos[2] >= brick_pos[0] and ball_pos[0] <= brick_pos[2] and ball_pos[3] >= brick_pos[1] and ball_pos[
                1] <= brick_pos[3]:  # Check if the ball hits the brick
                self.canvas.delete(brick)  # Remove the brick from the canvas
                self.bricks.remove(brick)  # Remove the brick from the list of bricks
                self.ball_dy = -self.ball_dy  # Reverse the vertical direction of the ball
                break  # Break the loop to avoid multiple collision detections


# Function to animate the introductory message and printing whatever i want
def animate_text(canvas, text_id, target_y):
    current_y = canvas.coords(text_id)[1]
    if current_y < target_y:
        canvas.move(text_id, 0, 2)
        canvas.after(15, animate_text, canvas, text_id, target_y)
    else:
        start_button = tk.Button(canvas.master, text="Game Start", command=start_game,
                                 font=('Helvetica', 14, 'bold'), bg='green', fg='white', padx=20, pady=10)
        start_button.place(x=CANVAS_WIDTH/2 - 70, y=current_y + 100)  # Place the button further below the text


# Function to start the game
def start_game():
    intro_root.destroy()  # Close the introductory window
    root = tk.Tk()  # Create the main window
    game = BrickBreaker(root)  # Create an instance of the BrickBreaker game
    root.mainloop()  # Run the main loop to start the game


# Main function to show the introductory message
if __name__ == '__main__':
    intro_root = tk.Tk()  # Create the main window
    intro_canvas = tk.Canvas(intro_root, width=CANVAS_WIDTH, height=CANVAS_HEIGHT,
                             bg='white')  # Create a canvas for the introduction
    intro_canvas.pack()  # Add the canvas to the window

    # Create the introductory message off-screen
    intro_message = intro_canvas.create_text(CANVAS_WIDTH / 2, -50,
                                             text="Hello there! I am Shahidul Islam Sawon\nStudent, Stanford University\nCode in Place CS106A Program - 2024\nThis is my final project",
                                             font=('Helvetica', 24, 'bold'), fill='blue', justify=tk.CENTER)

    # Animate the text into view
    animate_text(intro_canvas, intro_message, CANVAS_HEIGHT / 2)

    intro_root.mainloop()  # Run the main loop to show the introduction

#so that's it
