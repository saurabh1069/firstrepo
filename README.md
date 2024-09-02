<Window x:Class="EllipseShadowAnimation.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="Ellipse Shadow Animation on Hover" Height="400" Width="400">
    <Grid>
        <!-- Define a DropShadowEffect Resource -->
        <Grid.Resources>
            <DropShadowEffect x:Key="shadowEffect" BlurRadius="20" ShadowDepth="0" Color="Black" Opacity="0.5"/>
        </Grid.Resources>

        <!-- Ellipse with Shadow Effect -->
        <Ellipse Width="100" Height="100" Fill="Blue" Effect="{StaticResource shadowEffect}">
            <!-- Define Triggers for MouseEnter and MouseLeave -->
            <Ellipse.Style>
                <Style TargetType="Ellipse">
                    <Style.Triggers>
                        <!-- Trigger when mouse is over the ellipse -->
                        <Trigger Property="IsMouseOver" Value="True">
                            <Trigger.EnterActions>
                                <BeginStoryboard>
                                    <Storyboard>
                                        <!-- Animate the Scale Transform to make the ellipse "come out" -->
                                        <DoubleAnimation Storyboard.TargetProperty="(UIElement.RenderTransform).(ScaleTransform.ScaleX)"
                                                         From="1" To="1.2" Duration="0:0:0.3" />
                                        <DoubleAnimation Storyboard.TargetProperty="(UIElement.RenderTransform).(ScaleTransform.ScaleY)"
                                                         From="1" To="1.2" Duration="0:0:0.3" />
                                        <!-- Animate the shadow depth to create a realistic effect -->
                                        <DoubleAnimation Storyboard.TargetProperty="(UIElement.Effect).(DropShadowEffect.ShadowDepth)"
                                                         From="0" To="10" Duration="0:0:0.3" />
                                    </Storyboard>
                                </BeginStoryboard>
                            </Trigger.EnterActions>
                            <!-- Optional: Reverse the animation on mouse leave -->
                            <Trigger.ExitActions>
                                <BeginStoryboard>
                                    <Storyboard>
                                        <DoubleAnimation Storyboard.TargetProperty="(UIElement.RenderTransform).(ScaleTransform.ScaleX)"
                                                         From="1.2" To="1" Duration="0:0:0.3" />
                                        <DoubleAnimation Storyboard.TargetProperty="(UIElement.RenderTransform).(ScaleTransform.ScaleY)"
                                                         From="1.2" To="1" Duration="0:0:0.3" />
                                        <DoubleAnimation Storyboard.TargetProperty="(UIElement.Effect).(DropShadowEffect.ShadowDepth)"
                                                         From="10" To="0" Duration="0:0:0.3" />
                                    </Storyboard>
                                </BeginStoryboard>
                            </Trigger.ExitActions>
                        </Trigger>
                    </Style.Triggers>
                </Style>
            </Ellipse.Style>
            <!-- Apply a ScaleTransform to the RenderTransform to enable scaling -->
            <Ellipse.RenderTransform>
                <ScaleTransform ScaleX="1" ScaleY="1" />
            </Ellipse.RenderTransform>
        </Ellipse>
    </Grid>
</Window>