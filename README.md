<Window x:Class="EllipseShadowAnimation.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="Ellipse Shadow Animation on Hover" Height="400" Width="400">
    <Grid>
        <!-- Define a DropShadowEffect Resource -->
        <Grid.Resources>
            <DropShadowEffect x:Key="shadowEffect" BlurRadius="20" ShadowDepth="0" Color="Black" Opacity="0.5"/>

            <!-- Define the Style for the Ellipse with Triggers -->
            <Style x:Key="AnimatedEllipseStyle" TargetType="Ellipse">
                <Setter Property="Width" Value="100"/>
                <Setter Property="Height" Value="100"/>
                <Setter Property="Fill" Value="Blue"/>
                <Setter Property="Effect" Value="{StaticResource shadowEffect}"/>
                <Setter Property="RenderTransform">
                    <Setter.Value>
                        <ScaleTransform ScaleX="1" ScaleY="1"/>
                    </Setter.Value>
                </Setter>
                <!-- Triggers to animate on hover -->
                <Style.Triggers>
                    <!-- Trigger when mouse is over the ellipse -->
                    <Trigger Property="IsMouseOver" Value="True">
                        <Trigger.EnterActions>
                            <BeginStoryboard>
                                <Storyboard>
                                    <!-- Animate the Scale Transform to make the ellipse "come out" -->
                                    <DoubleAnimation Storyboard.TargetProperty="(UIElement.RenderTransform).(ScaleTransform.ScaleX)"
                                                     To="1.2" Duration="0:0:0.3" />
                                    <DoubleAnimation Storyboard.TargetProperty="(UIElement.RenderTransform).(ScaleTransform.ScaleY)"
                                                     To="1.2" Duration="0:0:0.3" />
                                    <!-- Animate the shadow depth to create a realistic effect -->
                                    <DoubleAnimation Storyboard.TargetProperty="(UIElement.Effect).(DropShadowEffect.ShadowDepth)"
                                                     To="10" Duration="0:0:0.3" />
                                </Storyboard>
                            </BeginStoryboard>
                        </Trigger.EnterActions>
                        <!-- Optional: Reverse the animation on mouse leave -->
                        <Trigger.ExitActions>
                            <BeginStoryboard>
                                <Storyboard>
                                    <DoubleAnimation Storyboard.TargetProperty="(UIElement.RenderTransform).(ScaleTransform.ScaleX)"
                                                     To="1" Duration="0:0:0.3" />
                                    <DoubleAnimation Storyboard.TargetProperty="(UIElement.RenderTransform).(ScaleTransform.ScaleY)"
                                                     To="1" Duration="0:0:0.3" />
                                    <DoubleAnimation Storyboard.TargetProperty="(UIElement.Effect).(DropShadowEffect.ShadowDepth)"
                                                     To="0" Duration="0:0:0.3" />
                                </Storyboard>
                            </BeginStoryboard>
                        </Trigger.ExitActions>
                    </Trigger>
                </Style.Triggers>
            </Style>
        </Grid.Resources>

        <!-- Ellipse with the defined Style -->
        <Ellipse Style="{StaticResource AnimatedEllipseStyle}" />
    </Grid>
</Window>