<Window x:Class="YourNamespace.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="MainWindow" Height="350" Width="525">
    <Grid HorizontalAlignment="Center" VerticalAlignment="Center">
        <Grid.Resources>
            <!-- Define a DropShadowEffect Resource -->
            <DropShadowEffect x:Key="shadowEffect" BlurRadius="28" ShadowDepth="10" Color="Black" Opacity="0.5"/>
            
            <!-- Define the Style for the Ellipse with Triggers -->
            <Style x:Key="AnimatedEllipseStyle" TargetType="Ellipse">
                <Setter Property="Width" Value="180"/>
                <Setter Property="Height" Value="100"/>
                <Setter Property="Fill" Value="Blue"/>
                <Setter Property="Effect" Value="{StaticResource shadowEffect}"/>
                <Setter Property="RenderTransform">
                    <Setter.Value>
                        <ScaleTransform ScaleX="1" ScaleY="1"/>
                    </Setter.Value>
                </Setter>
                <Style.Triggers>
                    <!-- Trigger when mouse is over the ellipse -->
                    <Trigger Property="IsMouseOver" Value="True">
                        <Trigger.EnterActions>
                            <BeginStoryboard>
                                <Storyboard>
                                    <!-- Animate the Scale Transform to make the ellipse "come out" -->
                                    <DoubleAnimation Storyboard.TargetProperty="(UIElement.RenderTransform).(ScaleTransform.ScaleX)"
                                                     To="1.2" Duration="0:0:0.3"/>
                                    <DoubleAnimation Storyboard.TargetProperty="(UIElement.RenderTransform).(ScaleTransform.ScaleY)"
                                                     To="1.2" Duration="0:0:0.3"/>
                                    <!-- Animate the shadow depth to create a realistic effect -->
                                    <DoubleAnimation Storyboard.TargetProperty="(UIElement.Effect).(DropShadowEffect.ShadowDepth)"
                                                     To="20" Duration="0:0:0.3"/>
                                </Storyboard>
                            </BeginStoryboard>
                        </Trigger.EnterActions>
                        <!-- Optional: Reverse the animation on mouse leave -->
                        <Trigger.ExitActions>
                            <BeginStoryboard>
                                <Storyboard>
                                    <DoubleAnimation Storyboard.TargetProperty="(UIElement.RenderTransform).(ScaleTransform.ScaleX)"
                                                     To="1" Duration="0:0:0.3"/>
                                    <DoubleAnimation Storyboard.TargetProperty="(UIElement.RenderTransform).(ScaleTransform.ScaleY)"
                                                     To="1" Duration="0:0:0.3"/>
                                    <DoubleAnimation Storyboard.TargetProperty="(UIElement.Effect).(DropShadowEffect.ShadowDepth)"
                                                     To="10" Duration="0:0:0.3"/>
                                </Storyboard>
                            </BeginStoryboard>
                        </Trigger.ExitActions>
                    </Trigger>
                </Style.Triggers>
            </Style>
        </Grid.Resources>

        <!-- Ellipse that uses the defined style -->
        <Ellipse Style="{StaticResource AnimatedEllipseStyle}"
                 Stroke="Black" StrokeThickness="3" RenderTransformOrigin="0.5,0.5"/>
    </Grid>
</Window>