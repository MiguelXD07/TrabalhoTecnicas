# Trabalho Técnicas
## Créditos: https://github.com/loveskold/2DPlatformer/tree/main

Grupo:\
Guilherme Carneiro nº31463\
Miguel Ferreira nº31472\
Nuno Rafael Oliveira Santos nº33191

O jogo é do tipo platformer, semelhante ao jogo Super Mario,...

Os ficheiros do jogo estão divididos em pastas, sendo mais simples de corrigir erros.

![image](https://github.com/user-attachments/assets/14141022-fb43-4abf-b44e-f8dc71bce449)

![image](https://github.com/user-attachments/assets/c24d301b-cc8b-43a6-9c0d-a70581bfc654)

Dentro da pasta content o criador tem os sprites dos personagens e objetos juntamente com musica e sound effects para o jogo.

![image](https://github.com/user-attachments/assets/da4bc1ec-0a7f-481b-9350-f39a3fc4878c)

------------------------------------------------------------------------------------------------------------------------------------
## Controls Classes:

Ficheiro que cria os botões de todos os menus.

### -Button
A classe Button onde é feita a criação de um retângulo com as dimensões do botão e todas as informações como texto, fonte etc. são alocadas em variáveis. Depois disso é chamada a função Update para verificar a cada frame se o cursor (que está contido dentro de um retângulo que também é criado a cada frame) intercepta o retângulo criado anteriormente, se sim troca a cor do background para cinzento escuro e a do texto para branco e depois verifica se o botão esquerdo do rato foi pressionado e largado para executar a ação do botão, senão a cor de fundo do retângulo continua a ser branco e o texto preto.

Por fim desenha os botões com a função draw!

------------------------------------------------------------------------------------------------------------------------------------
## Tiles Classes

### -BackgroundTile

Cria uma classe BackgroundTiles que cria os tiles com a respetiva textura e posição e por fim desenha-os.

### -Ladder

Cria a classe Ladder que cria as escadas com a textura, posição e hitbox correspondente e depois desenha a mesma.
------------------------------------------------------------------------------------------------------------------------------------
## Sprite Classes:

Dentro desta pasta, que não é a mesma que a pasta Sprites do Content, encontram-se as classes Animation, Coins, Enemy e Player.

### -Animation
Na class Animation o código atualiza a hitbox das animações.
Esta parte do código adiciona um retangulo (hitbox) para cada frame da spritesheet.
```
        public Animation(Texture2D newTexture, int numberOfFrames, float newFrameTime)
        {
            texture = newTexture;
            frameTime = newFrameTime;
            frameTimeLeft = frameTime;
            frames = numberOfFrames;

            var frameWidth = texture.Width / numberOfFrames;
            var frameHeight = texture.Height;

            for (int i = 0; i < frames; i++) // Lägger till en rektangel för varje frame i spritesheet
            {
                sourceRectangles.Add(new Rectangle(i * frameWidth, 0, frameWidth, frameHeight));
            }
        }
```

### -Coin
Na class Coin apenas tem o código para a animação e criação das moedas.

### -Enemy
Na class Enemy o código cria e gerencia as ações dos inimigos.
O criador colocou uma verificação em que se a hitbox do inimigo não encontrar chão, o mesmo muda de direção, utilizando a função Turn().
```
            intersected = false;
            foreach (Platform platform in GameState.platforms)
            {
                if (rectangleRight.Intersects(platform.rectangle) || rectangleLeft.Intersects(platform.rectangle))
                {
                    Turn();
                }
                else if (turningRectangle.Intersects(platform.rectangle))
                {
                    intersected = true;
                }
            }
            isGrounded = intersected;
            
            if (isGrounded == false) // Om turningRectangle inte längre rör plattformen vänder fienden håll
            {
                Turn();
            }

        public void Turn()
        {
            velocity.X = -velocity.X;
            if (movingRight)
            {
                movingRight = false;
            }
            else if (!movingRight)
            {
                movingRight = true;
            }
        }
```

### -Player
Na classe Player o código cria o personagem (player) e gerencia as ações de mexer e colisões do personagem com outros objetos.
O criador colocou um bloco de codigo em que faz com que o personagem salte mais alto depedendo do tempo segurado no espaço e cria gravidade para o personagem.

```
currentKeyboardState = Keyboard.GetState();
            if (previousKeyboardState.IsKeyDown(Keys.Space) && currentKeyboardState.IsKeyUp(Keys.Space) && velocity.Y < 0) // Varje gång man släpper Space
            {
                velocity.Y = -3f;
                hasJumped = true;
            }
            previousKeyboardState = currentKeyboardState;
            #endregion

            #region Gravity
            if (hasJumped && !touchingLadder)
            {
                velocity.Y += 0.5f;
                isGrounded = false;
            }

            if (touchingLadder && Keyboard.GetState().IsKeyUp(Keys.Space))
            {
                velocity.Y += 0.5f;
            }

            if (isGrounded == false)
            {
                hasJumped = true;
            }
            if (isGrounded && velocity.Y >= 0 && !touchingLadder)
            {
                velocity.Y = 0f;
            }
            if (position.Y > 720)
            {
                Die();
            }
            #endregion
```

Ainda na class Player o criador tem várias linhas de codiga para a colisão com os inimigos, moedas e plataformas.
```
            #region Kollisioner med coins

            for (int i = 0; i < GameState.coins.Count; i++)
            {
                if (rectangle.Intersects(GameState.coins[i].rectangle))
                {
                    GameState.coins.RemoveAt(i);
                    currentScore += 50;
                    coin_sound.Play(0.07f, 0, 0);
                    GameState.score_str = currentScore.ToString();
                }
            }

            #endregion

            #region Kollisioner med stegar

            bool intersectingLadder = false;
            foreach (Ladder ladder in GameState.ladders)
            {
                if (rectangle.Intersects(ladder.rectangle))
                {
                    intersectingLadder = true;
                }
            }
            touchingLadder = intersectingLadder;
```

------------------------------------------------------------------------------------------------------------------------------------
## States Classes:

### -State

Esta classe serve para ditar as propriedades que as classes State devem tomar.

```
public abstract class State 
    {
        protected ContentManager _content; 
        protected GraphicsDevice _graphics; 
        protected Game1 _game; 
        
        public State(Game1 game, GraphicsDevice graphicsDevice, ContentManager content)
        {
            _content = content;
            _graphics = graphicsDevice;
            _game = game;
        }

        public abstract void LoadContent();
        public abstract void Draw(GameTime gameTime, SpriteBatch spriteBatch);
        public abstract void Update(GameTime gameTime);
    }
```

### -MenuState

A classe MenuState é utilizada ao abrir o jogo.
Ela cria um background e dois butões, o botão Play e o botão Exit.
Escrito no ecrã tem o nome do jogo e os controlos.
Carregar em Play muda para e dá reset ao GameState.
Carregar em Exit fecha o jogo.

```
public MenuState(Game1 game,GraphicsDevice graphics, ContentManager content)
            : base(game, graphics, content)
        {
            font = _content.Load<SpriteFont>("Fonts/font");
            font_larger = _content.Load<SpriteFont>("Fonts/font_larger");
            font_smaller = _content.Load<SpriteFont>("Fonts/font_smaller");
            font_smallest = _content.Load<SpriteFont>("Fonts/font_smallest");

            button_texture = _content.Load<Texture2D>("Controls/button");
            background = _content.Load<Texture2D>("Sprites/menu-background");

            click_sound = _content.Load<SoundEffect>("Sound Effects/interface1");

            button_play = new Button(button_texture, new Vector2(Game1.screenWidth / 2 - button_texture.Width / 2, Game1.screenHeight / 2 - button_texture.Height / 2), font, "Play");
            button_exit = new Button(button_texture, new Vector2(Game1.screenWidth / 2 - button_texture.Width / 2, Game1.screenHeight / 2 - button_texture.Height / 2 + 150), font, "Exit");
        }

public override void Draw(GameTime gameTime, SpriteBatch spriteBatch)
        {
            _game.GraphicsDevice.Clear(Color.Green);
            spriteBatch.Begin();
            spriteBatch.Draw(background, new Vector2(0, 0), Color.White);
            button_play.Draw(spriteBatch);
            button_exit.Draw(spriteBatch);
            spriteBatch.DrawString(font_larger, "2DPlatformer", new Vector2(1280 / 2 - 250, 150), Color.White);
            spriteBatch.DrawString(font_smaller, "Controls:", new Vector2(100, 150), Color.White);
            spriteBatch.DrawString(font_smallest, "Move Left: A / LeftArrow", new Vector2(100 , 185), Color.White);
            spriteBatch.DrawString(font_smallest, "Move Right: D / RightArrow", new Vector2(100, 205), Color.White);
            spriteBatch.DrawString(font_smallest, "Jump: Space", new Vector2(100, 225), Color.White);
            spriteBatch.DrawString(font_smallest, "Climb Ladder: Hold Space", new Vector2(100, 245), Color.White);
            spriteBatch.DrawString(font_smallest, "Exit to menu: Escape", new Vector2(100, 265), Color.White);
            spriteBatch.End();
        }

```

### -GameState

Esta classe engloba todo o gameplay fora dos menus.
A classe cria e carrega todos os elementos a ser utilizados durante o gameplay.
A classe dita também as condições associadas ao jogo como a posição da camara, o temporizador, a posição de diferentes elementos e o estado do jogador.
Esta classe é portanto o culminar de todas as outras classes e dos outros elementos como tiles, texturas e efeitos sonoros.

```
 	public static List<Platform> platforms = new List<Platform>();
        public static List<Enemy> enemies = new List<Enemy>();
        public static List<Coin> coins = new List<Coin>();
        public static List<MovingPlatform> movingPlatforms = new List<MovingPlatform>();
        public static List<BackgroundTile> backgroundTiles = new List<BackgroundTile>();
        public static List<Ladder> ladders = new List<Ladder>();

        public static Texture2D grass2_texture, dirt_texture, grass_texture, grass3_texture, grass4_texture,
                                spike_texture, spike_right_texture, spike_top_texture,
                                sign_texture, spring_texture, coin_texture, ladder_texture, box_texture,
                                player_texture, player_walking_texture, player_jumping_texture,
                                enemy_walking_texture,
                                cloud_texture, water1_texture, water2_texture, mushroom_texture, plant1_texture, plant2_texture, plant3_texture;

        public static SoundEffect jump_sound, enemy_death_sound, coin_sound, player_death_sound, win_sound, clock_sound;
        SoundEffectInstance clock_soundInstance;


        Song music;

        SpriteFont font;
        public static string score_str = "0";
        public static string timer_str = "60s";
        public static Vector2 score_pos, timer_pos;

        public static Player player;
        Map map;
        Camera camera;

        float timer = 100;
        bool clockSoundPlayed = false;
```


### -DeadState

A classe DeadState é utilizada quando o jogador morre.
Ela muda o background para uma tela preta que diz que o jogador morreu e cria dois botões, um para dar respawn e outro para voltar para o menu.
O botão de respawn dá reset a todos os gamestates e reinicia o jogo.
O botão de menu volta para o menu principal (MenuState).

```
public override void LoadContent()
        {
            font = _content.Load<SpriteFont>("Fonts/font");
            font_larger = _content.Load<SpriteFont>("Fonts/font_larger");
            button_texture = _content.Load<Texture2D>("Controls/button");
            click_sound = _content.Load<SoundEffect>("Sound Effects/interface1");
            background = _content.Load<Texture2D>("Sprites/deadstate-background");

            button_respawn = new Button(button_texture, new Vector2(Game1.screenWidth / 2 - button_texture.Width / 2, Game1.screenHeight / 2 - button_texture.Height / 2), font, "Retry");
            button_menu = new Button(button_texture, new Vector2(Game1.screenWidth / 2 - button_texture.Width / 2, Game1.screenHeight / 2 - button_texture.Height / 2 + 150), font, "Menu");

            MediaPlayer.Stop(); // Stannar musiken
        }

        public override void Update(GameTime gameTime)
        {
            button_respawn.Update();
            if (button_respawn.clicked)
            {
                click_sound.Play();
                Thread.Sleep(200);
                GameState.enemies.Clear();
                GameState.coins.Clear();
                GameState.platforms.Clear();
                GameState.movingPlatforms.Clear();
                GameState.backgroundTiles.Clear();
                savedPlayerLevel = GameState.player.level;
                savedPlayerScore = GameState.player.savedScore;
                _game.ChangeState(new GameState(_game, _graphics, _content));
                _game.IsMouseVisible = false;
            }

            button_menu.Update();
            if (button_menu.clicked)
            {
                click_sound.Play();
                Thread.Sleep(200);
                _game.ChangeState(new MenuState(_game, _graphics, _content));
            }
        }

        public override void Draw(GameTime gameTime, SpriteBatch spriteBatch)
        {
            spriteBatch.Begin();
            spriteBatch.Draw(background, new Vector2(0, 0), Color.White);
            button_menu.Draw(spriteBatch);
            button_respawn.Draw(spriteBatch);
            spriteBatch.DrawString(font_larger, "You Died!", new Vector2(1280 / 2 - 150, 150), Color.Red);
            spriteBatch.End();
        }
```

### -EndState

A classe EndState é utilizada quando o jogador ganha o jogo.
A classe demonstra o Score e os Highscores passados.
Cria um botão Menu para voltar para o MenuState.

```
public override void Draw(GameTime gameTime, SpriteBatch spriteBatch)
        {
            _game.GraphicsDevice.Clear(Color.Green);
            spriteBatch.Begin();
            spriteBatch.DrawString(font_larger, "You Win!", new Vector2(1280 / 2 - 100, 150), Color.White);
            spriteBatch.DrawString(font, $"Score: {GameState.player.currentScore} ", new Vector2(1280 / 2 - 100, 250), Color.White);

            spriteBatch.DrawString(font, "Highscores", new Vector2(900, 50), Color.White);
            for (int i = 0; i < Game1.scores.Count; i++)
            {
                spriteBatch.DrawString(font_smaller, $"{i + 1}.       " + Game1.scores[i].ToString(), new Vector2(1000, 50 * (i + 1) + 100), Color.White);
            }
            button.Draw(spriteBatch);
            spriteBatch.End();
        }
```
----------------------------------------------------------------------------------------------------------------------------------------------------------------
## Camera:

Na class Camera, o código faz com que a camera mova-se para esquerda e direita fiquando centralizada no jogador, parando de se mexer quando o jogador está nos limites do mapa.
Assim, a camara não passa da parede final ou inicial.
```
namespace _2DPlatformer
{
    internal class Camera
    {
        public Matrix Transform { get; private set; }
        public void Follow(Player target)
        {
            var position = Matrix.CreateTranslation( MathHelper.Clamp(-target.position.X - (target.rectangle.Width / 2), -3420, -650), MathHelper.Clamp(-target.position.Y - (target.rectangle.Height / 2), -300, -3475738), 0);
            var offset = Matrix.CreateTranslation(Game1.screenWidth / 2, Game1.screenHeight / 2 - 100, 0);
            Transform = position * offset;
        }
    }
}
```
----------------------------------------------------------------------------------------------------------------------------------------------------------------
## Game1:

Dentro da classe Game1, o código define as dimensões da janela.
```
       public Game1()
        {
            _graphics = new GraphicsDeviceManager(this);
            _graphics.PreferredBackBufferWidth = screenWidth; 
            _graphics.PreferredBackBufferHeight = screenHeight;  
            _graphics.ApplyChanges();
            Content.RootDirectory = "Content";
            IsMouseVisible = true;
        }
```

Cria um temporizador de 100 segundos para cada nivel.
```
 protected override void Update(GameTime gameTime)
        {
            TotalSeconds = (float)gameTime.ElapsedGameTime.TotalSeconds;
            if (_nextState != null) // Om nextState har ett värde ska en ny state visas
            {
                _currentState = _nextState;
                _currentState.LoadContent(); // Laddar in nya staten
                _nextState = null;
            }
            _currentState.Update(gameTime); // Uppdaterar aktuella staten
            base.Update(gameTime);
        }
```

Muda os niveis e tem uma função para sair do jogo.
```
 public void ChangeState(State state)
        {
            _nextState = state;
        }
        public void Quit()
        {
            this.Exit();
        }
        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.LightSkyBlue);
            _currentState.Draw(gameTime, _spriteBatch); // Drawar aktuella staten
            base.Draw(gameTime);
        }
    }
}
```
-------------------------------------------------------------------------------------------------------------------------------------
