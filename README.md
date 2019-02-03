https://github.com/JaideepYadav910/Water-the-plant-GAME/blob/master/sample.jpg

# Water-the-plant-GAME
# An game made using python's pygames and livewires modules.


from livewires import games, color
import random

games.init(screen_width = 640, screen_height = 480, fps = 50)


class Plant(games.Sprite):

    """
    A plant controlled by player to catch falling water drops.
    """
    
    image = games.load_image("plant.bmp")

    def __init__(self):
    
        """ Initialize Plant object and create Text object for score. """
        
        super(Plant, self).__init__(image = Plant.image,
                                  x = games.mouse.x,
                                  bottom = games.screen.height)
        
        self.score = games.Text(value = 0, size = 25, color = color.black,
                                top = 5, right = games.screen.width - 10)
        games.screen.add(self.score)

    def update(self):
    
        """ Move to mouse x position. """
        
        self.x = games.mouse.x
        
        if self.left < 0:
            self.left = 0
            
        if self.right > games.screen.width:
            self.right = games.screen.width
            
        self.check_catch()

    def check_catch(self):
    
        """ Check if catch water drop."""
        
        for water in self.overlapping_sprites:
            self.score.value += 10
            self.score.right = games.screen.width - 10 
            water.handle_caught()


class Water(games.Sprite):

    """
    A water drop falling to ground.
    """
    
    image = games.load_image("water.bmp")
    speed = 2   

    def __init__(self, x, y = 90):
    
        """ Initialize a Water object. """ 
        
        super(Water, self).__init__(image = Water.image,
                                    x = x, y = y,
                                    dy = Water.speed )

    def update(self):
    
        """ Check if bottom edge has reached screen bottom. """
        
        if self.bottom > games.screen.height:
            self.end_game()
            self.destroy()

    def handle_caught(self):
    
        """ Destroy self if caught. """
        
        self.destroy()

    def end_game(self):
    
        """ End the game. """
        
        end_message = games.Message(value = "Game Over",
                                    size = 90,
                                    color = color.red,
                                    x = games.screen.width/2,
                                    y = games.screen.height/2,
                                    lifetime = 5 * games.screen.fps,
                                    after_death = games.screen.quit)
        games.screen.add(end_message)

class Water_Man(games.Sprite):

    """
    A water_man which moves left and right, dropping water_drops.
    """
    
    image = games.load_image("water_man.png")

    def __init__(self, y = 55, speed = 5, odds_change = 200):
    
        """ Initialize the water_man object. """ 
        
        super(Water_Man, self).__init__(image = Water_Man.image,
                                   x = games.screen.width / 2,
                                   y = y,
                                   dx = speed)
        
        self.odds_change = odds_change
        self.time_til_drop = 0

    def update(self):
    
        """ Determine if direction needs to be reversed. """
        
        if self.left < 0 or self.right > games.screen.width:
            self.dx = -self.dx
        elif random.randrange(self.odds_change) == 0:
           self.dx = -self.dx
                
        self.check_drop()


    def check_drop(self):
         
        if self.time_til_drop > 0:
            self.time_til_drop -= 1
        else:
            new_water_drop = Water(x = self.x)
            games.screen.add(new_water_drop)

               
            self.time_til_drop = int(new_water_drop.height * 1.3 / Water.speed) + 1      



def main():

    """ Play the game. """
    
    wall_image = games.load_image("wall.jpg", transparent = False)
    games.screen.background = wall_image

    Water_man = Water_Man()
    games.screen.add(Water_man)
  
    the_plant = Plant()
    games.screen.add(the_plant)

    games.mouse.is_visible = False

    games.screen.event_grab = True
    games.screen.mainloop()

# start it up!
main()

