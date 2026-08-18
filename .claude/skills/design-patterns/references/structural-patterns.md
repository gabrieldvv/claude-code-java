# Structural Patterns

## Structural Patterns

### Decorator

**Use when:** Add behavior dynamically without modifying existing classes.

```java
// ✅ Decorator pattern
public interface Coffee {
    String getDescription();
    BigDecimal getCost();
}

public class SimpleCoffee implements Coffee {
    @Override
    public String getDescription() {
        return "Coffee";
    }

    @Override
    public BigDecimal getCost() {
        return new BigDecimal("2.00");
    }
}

// Base decorator
public abstract class CoffeeDecorator implements Coffee {
    protected final Coffee coffee;

    public CoffeeDecorator(Coffee coffee) {
        this.coffee = coffee;
    }

    @Override
    public String getDescription() {
        return coffee.getDescription();
    }

    @Override
    public BigDecimal getCost() {
        return coffee.getCost();
    }
}

// Concrete decorators
public class MilkDecorator extends CoffeeDecorator {
    public MilkDecorator(Coffee coffee) {
        super(coffee);
    }

    @Override
    public String getDescription() {
        return coffee.getDescription() + ", Milk";
    }

    @Override
    public BigDecimal getCost() {
        return coffee.getCost().add(new BigDecimal("0.50"));
    }
}

public class SugarDecorator extends CoffeeDecorator {
    public SugarDecorator(Coffee coffee) {
        super(coffee);
    }

    @Override
    public String getDescription() {
        return coffee.getDescription() + ", Sugar";
    }

    @Override
    public BigDecimal getCost() {
        return coffee.getCost().add(new BigDecimal("0.20"));
    }
}

public class WhippedCreamDecorator extends CoffeeDecorator {
    public WhippedCreamDecorator(Coffee coffee) {
        super(coffee);
    }

    @Override
    public String getDescription() {
        return coffee.getDescription() + ", Whipped Cream";
    }

    @Override
    public BigDecimal getCost() {
        return coffee.getCost().add(new BigDecimal("0.70"));
    }
}

// Usage - compose decorators
Coffee coffee = new SimpleCoffee();
coffee = new MilkDecorator(coffee);
coffee = new SugarDecorator(coffee);
coffee = new WhippedCreamDecorator(coffee);

System.out.println(coffee.getDescription());  // Coffee, Milk, Sugar, Whipped Cream
System.out.println(coffee.getCost());         // 3.40
```

**Java I/O uses Decorator:**
```java
// Classic example from Java
BufferedReader reader = new BufferedReader(
    new InputStreamReader(
        new FileInputStream("file.txt")
    )
);
```

---

### Adapter

**Use when:** Make incompatible interfaces work together.

```java
// ✅ Adapter pattern

// Existing interface our code uses
public interface MediaPlayer {
    void play(String filename);
}

// Legacy/third-party interface
public class LegacyAudioPlayer {
    public void playMp3(String filename) {
        System.out.println("Playing MP3: " + filename);
    }
}

public class AdvancedVideoPlayer {
    public void playMp4(String filename) {
        System.out.println("Playing MP4: " + filename);
    }

    public void playAvi(String filename) {
        System.out.println("Playing AVI: " + filename);
    }
}

// Adapters
public class Mp3PlayerAdapter implements MediaPlayer {
    private final LegacyAudioPlayer legacyPlayer = new LegacyAudioPlayer();

    @Override
    public void play(String filename) {
        legacyPlayer.playMp3(filename);
    }
}

public class VideoPlayerAdapter implements MediaPlayer {
    private final AdvancedVideoPlayer videoPlayer = new AdvancedVideoPlayer();

    @Override
    public void play(String filename) {
        if (filename.endsWith(".mp4")) {
            videoPlayer.playMp4(filename);
        } else if (filename.endsWith(".avi")) {
            videoPlayer.playAvi(filename);
        }
    }
}

// Usage
MediaPlayer mp3Player = new Mp3PlayerAdapter();
mp3Player.play("song.mp3");

MediaPlayer videoPlayer = new VideoPlayerAdapter();
videoPlayer.play("movie.mp4");
```
