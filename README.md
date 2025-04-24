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
