---
title: Wzorce projektowe - Strategia
layout: post
categories:
- Wzorce projektowe
---

Wzorzec strategii należy do grupy wzorców czynnościowych.

Pozwala on na zmianę działania pewnego zachowania wewnątrz klasy po przez ustawienie danej "strategii".

## Przykład

Przykładem będzie klasa wroga, która będzie zawierać rodzaj ataku. Rodzaj ataku będzie tutaj strategią. 
Wróg może mieć kilka rodzajów ataku np. normalny, magiczny, dystansowy itd.

Klasa Enemy w konstruktorze pobiera AttackType.

{% highlight java %}
public class Enemy {

    private AttackType attackType;

    public Enemy(AttackType attackType) {
        this.attackType = attackType;
    }

    public AttackType getAttackType() {
        return attackType;
    }

    public void setAttackType(AttackType attackType) {
        this.attackType = attackType;
    }
}

{% endhighlight %}

AttackType jest interfejsem, który będą implementować klasy będące rodzajem ataku.

{% highlight java %}

public interface AttackType {
    void attack();

    class NormalAttack implements AttackType {

        @Override
        public void attack() {
            System.out.println("Normal attack!");
        }
    }

    class MagicAttack implements AttackType {

        @Override
        public void attack() {
            System.out.println("Magic attack!");
        }
    }

    class DistanceAttack implements AttackType {

        @Override
        public void attack() {
            System.out.println("Distance attack!");
        }
    }
}

{% endhighlight %}

Dla przykładu zostaną stworzeni dwa rodzaje wrogów. Łucznik oraz mag. 
Łucznik będzie atakować na dystans, a mag będzie miał atak magiczny.

Klasy te będą dziedziczyć po klasie Enemy.

{% highlight java %}

public class Archer extends Enemy {

    public Archer(AttackType attackType) {
        super(attackType);
    }
}


public class Mage extends Enemy {
    
    public Mage(AttackType attackType) {
        super(attackType);
    }
}


{% endhighlight %}

#### Wykorzystanie

{% highlight java %}

Enemy archer = new Archer(new AttackType.DistanceAttack());
Enemy mage = new Mage(new AttackType.MagicAttack());

archer.getAttackType().attack();
mage.getAttackType().attack();

{% endhighlight %}

## Diagram

![](/assets/strategy.png)


## Zalety / Wady

#### Zalety

1. Zdefiniowanie konkretnych zachowań jako klasy;
2. Zmiana zachowania dynamicznie;
3. Redukcja dużych bloków if na mniejsze zachowania w klasach;
4. Unikanie duplikowania kodu;
5.  Zmiana zmiana działa kodu zmienia się w jednej klasie i nie ma wpływu na inne;

#### Wady

1. Zwiększa się ilość klas