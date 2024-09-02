<Window x:Class="YourNamespace.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="MainWindow" Height="350" Width="525">
    <Grid HorizontalAlignment="Center" VerticalAlignment="Center">
        <Grid.Resources>
            <!-- Define a DropShadowEffect Resource -->
            <DropShadowEffect x:Key="shadowEffect" BlurRadius="28" ShadowDepth="10" Color="Black" Opacity="0.5"/>

            <!-- Define the Style for the Outer Ellipse with Triggers -->
            <Style x:Key="AnimatedEllipseStyle" TargetType="Ellipse">
                <Setter Property="Width" Value="180"/>
                <Setter Property="Height" Value="100"/>
                <Setter Property="Fill" Value="White"/> <!-- Set ellipse fill color to white -->
                <Setter Property="Stroke" Value="Black"/> <!-- Set stroke color to black -->
                <Setter Property="StrokeThickness" Value="3"/> <!-- Set stroke thickness -->
                <Setter Property="Effect" Value="{StaticResource shadowEffect}"/>
                <Setter Property="RenderTransformOrigin" Value="0.5,0.5"/> <!-- Correct origin for transformations -->
                <Setter Property="RenderTransform">
                    <Setter.Value>
                        <TransformGroup>
                            <ScaleTransform ScaleX="1" ScaleY="1"/>
                            <SkewTransform AngleX="0" AngleY="0"/>
                            <RotateTransform Angle="0"/>
                            <TranslateTransform X="0" Y="0"/>
                        </TransformGroup>
                    </Setter.Value>
                </Setter>
                <Style.Triggers>
                    <!-- Trigger when mouse is over the ellipse -->
                    <Trigger Property="IsMouseOver" Value="True">
                        <Trigger.EnterActions>
                            <BeginStoryboard>
                                <Storyboard>
                                    <!-- Animate the Scale Transform to make the ellipse "come out" -->
                                    <DoubleAnimation Storyboard.TargetProperty="(UIElement.RenderTransform).(TransformGroup.Children)[0].(ScaleTransform.ScaleX)"
                                                     To="1.2" Duration="0:0:0.3"/>
                                    <DoubleAnimation Storyboard.TargetProperty="(UIElement.RenderTransform).(TransformGroup.Children)[0].(ScaleTransform.ScaleY)"
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
                                    <DoubleAnimation Storyboard.TargetProperty="(UIElement.RenderTransform).(TransformGroup.Children)[0].(ScaleTransform.ScaleX)"
                                                     To="1" Duration="0:0:0.3"/>
                                    <DoubleAnimation Storyboard.TargetProperty="(UIElement.RenderTransform).(TransformGroup.Children)[0].(ScaleTransform.ScaleY)"
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

        <!-- Outer Ellipse that uses the defined style -->
        <Ellipse Style="{StaticResource AnimatedEllipseStyle}">
            <!-- Inner Ellipse with Image Fill -->
            <Ellipse Margin="32,22,32,22">
                <Ellipse.Fill>
                    <ImageBrush ImageSource="Images/Ideas_new.png"/> <!-- Path to the image file -->
                </Ellipse.Fill>
            </Ellipse>
        </Ellipse>
    </Grid>
</Window>