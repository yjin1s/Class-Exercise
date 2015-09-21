# Class-Exercise
using System;
using System.Collections.Generic;
using System.Linq;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Audio;
using Microsoft.Xna.Framework.Content;
using Microsoft.Xna.Framework.GamerServices;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Input;
using Microsoft.Xna.Framework.Media;

namespace WindowsGame2
{
    /// <summary>
    /// This is the main type for your game
    /// </summary>
    public class Game1 : Microsoft.Xna.Framework.Game
    {
        String curState = "CHASING";
        GraphicsDeviceManager graphics;
        SpriteBatch spriteBatch;
        Texture2D player;
        Texture2D enemy;
        Vector2 playerPos;
        Vector2 enemyPos;
        Vector2 cavePos;
        Vector2 speed = new Vector2(0, 0);
        int click = 0;
        float angle = 0;
        float threshold = 500;


        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            Content.RootDirectory = "Content";

        }


        protected override void Initialize()
        {
            // TODO: Add your initialization logic here

            this.IsMouseVisible = true;


            base.Initialize();
        }

        protected override void LoadContent()
        {
            // Create a new SpriteBatch, which can be used to draw textures.
            spriteBatch = new SpriteBatch(GraphicsDevice);

            player = Content.Load<Texture2D>(@"player");
            enemy = Content.Load<Texture2D>(@"enemy");

            // TODO: use this.Content to load your game content here
        }


        protected override void UnloadContent()
        {
            // TODO: Unload any non ContentManager content here
        }

        protected override void Update(GameTime gameTime)
        {
            // Allows the game to exit
            if (GamePad.GetState(PlayerIndex.One).Buttons.Back == ButtonState.Pressed)
                this.Exit();

            // TODO: Add your update logic here

            MouseState mouse = Mouse.GetState();

            if (mouse.LeftButton == ButtonState.Pressed)
                click++;

            playerPos = new Vector2(mouse.X, mouse.Y);
            cavePos = new Vector2(graphics.GraphicsDevice.Viewport.Width / 2,
                graphics.GraphicsDevice.Viewport.Height / 2);
            enemyPos = new Vector2(graphics.GraphicsDevice.Viewport.Width / 2,
                graphics.GraphicsDevice.Viewport.Height / 2);



            if (curState == "IDLE")
            {
                if (threshold > countDistance(enemyPos, playerPos))
                    curState = "CHASING";
            }
            else if (curState == "CHASING")
            {
                if (threshold < countDistance(enemyPos, cavePos))
                    curState = "RETREATING";
                else
                {
                    angle = toAngle(enemyPos, playerPos);
                    speed = countSpeed(angle);
                    enemyPos += speed * (float)gameTime.ElapsedGameTime.TotalSeconds;
                    if (threshold < countDistance(enemyPos, cavePos))
                        curState = "RETREATING";
                }
            }

            else if (curState == "RETRETING")
            {
                if (cavePos == enemyPos)
                    curState = "IDLE";
                else
                {
                    angle = toAngle(enemyPos, cavePos);
                    speed = countSpeed(angle);
                    enemyPos += speed * (float)gameTime.ElapsedGameTime.TotalSeconds;
                    ;
                }

                if (threshold > countDistance(enemyPos, playerPos))
                    curState = "CHASING";
            }



            base.Update(gameTime);
        }

        /// <summary>
        /// This is called when the game should draw itself.
        /// </summary>
        /// <param name="gameTime">Provides a snapshot of timing values.</param>
        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            // TODO: Add your drawing code here
            spriteBatch.Begin();
            if (click >= 1)
            {
                spriteBatch.Draw(player, playerPos, Color.CornflowerBlue);
                spriteBatch.Draw(enemy, enemyPos, Color.CornflowerBlue);
            }
            spriteBatch.End();

            base.Draw(gameTime);
        }

        public float toAngle(Vector2 vector1, Vector2 vector2)
        {
            return (float)Math.Atan2(vector2.Y - vector1.Y, vector2.X - vector1.X);
        }

        public Vector2 countSpeed(float angle)
        {
            return new Vector2((float)Math.Sin(angle) * 500, (float)Math.Cos(angle) * 500);
        }

        public float countDistance(Vector2 vector1, Vector2 vector2)
        {
            return Vector2.Distance(vector2, vector1);
        }


    }
}
