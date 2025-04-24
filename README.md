# Trabalho Tecnicas
## Créditos: https://github.com/loveskold/2DPlatformer/tree/main

Grupo:\
Guilherme Carneiro nº31463\
Miguel Ferreira nº31472\
Nuno Rafael Oliveira Santos nº33191

O jogo é do tipo platformer, semelhante ao jogo Super Mario,...



Na classe Player, o criador colocou um bloco de codigo em que faz com que o personagem salte mais alto depedendo do tempo carregado no espaço.
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


------------------------------------------------------------------------------------------------------------------------------------
States Classes:

-State

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

-MenuState

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

-GameState

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


-DeadState

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

-EndState

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
