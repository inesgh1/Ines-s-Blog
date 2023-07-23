---
layout: post
title: A Data Scientist's Journey into the Realm of Love!💘
date: 2023-07-21
description: This is a project that explore the speed dating data 
img: lovedata.jpeg # Add image post (optional)
tags: [Programming,K-means,EDA,speed dating ,data science] # add tag
---
# Unraveling the Secrets of Speed Dating: A Data Scientist's Journey into the Realm of Love! 💘

As a single data scientist, I couldn't resist the allure of putting the power of data science into the world of love and romance. Join me as I embark on a whimsical and enlightening analysis of speed dating data, revealing the quirky patterns and fascinating statistics that govern our hearts.
## The speed dating dataset
The dataset speed dating data is quite substantial — over 8,000 observations with almost 200 datapoints for each. In 2002–2004, Columbia University ran a speed-dating experiment where they tracked 21 speed dating sessions for mostly young adults meeting people of the opposite sex. I found the dataset and the key to the data here: [dataset](http://www.stat.columbia.edu/~gelman/arm/examples/speed.dating/).

## Age Distribution: Is Love a Matter of Timing?

Let's begin with age, the timeless factor in the dating universe. After analyzing the data, it turns out that most of the participants in the speed dating experiment were in their mid-twenties to early thirties. Ah, the prime time for romantic adventures! So folks, if you're feeling that the clock is ticking, worry not! You're right on track with the dating universe's age standards.

![Age Distribution](assets/img/image.png)

## Gender Dynamics: A Dance of Romantic Timing

But wait, the gender differences add a delightful twist to the tale. While ladies dominated the scene in their twenties, the gents had their moment in the early thirties. A dance of romantic timing, wouldn't you say?

```python
# Define custom labels for the legend
gender_labels = ['Female', 'Male']

# Define custom colors for the legend
gender_colors = {'Female': 'hotpink', 'Male': 'darkblue'}

# Create the FacetGrid
g = sns.FacetGrid(data, hue='gender', height=6, palette=[gender_colors[label] for label in gender_labels])

# Plot the histogram
g.map(plt.hist, 'age', alpha=0.7, bins=20)

# Set x-axis label
g.set_xlabels('Age', fontsize=12)

# Set title for the entire plot
plt.subplots_adjust(top=0.9)
g.fig.suptitle('Age distribution by gender', fontsize=15)

# Add custom legend with labels and colors
legend = g.add_legend(title='Gender', labels=gender_labels)


# Show the plot
plt.show()

```

## The Quest for Love: Matchmaking Success Rate

Now, let's dive into the matchmaking success rate. Brace yourselves for this one! The majority of speed dating interactions didn't lead to a match. But hey, don't be disheartened! Love might be elusive, but it's not impossible. As they say, it's not about the number of tries; it's about the magical moment when two hearts connect.
```python
#before we start let's see how many of the participant found their match❤️‍🔥
matches_count = data['match'].value_counts()
print("Frequency of Matches:")
print(matches_count)

# Plot the histogram
plt.figure(figsize=(8, 6))
plt.bar(matches_count.index, matches_count.values, color=['red', 'green'])
plt.xlabel('Match')
plt.ylabel('Count')
plt.title('Histogram of Match Counts (Yes and No)')
plt.xticks([0, 1], ['No', 'Yes'])
plt.show()
```
![Matchmaking Success Rate](agedistbygender.png)
These initial statistics show that the majority of speed dating interactions did not lead to a match, while a smaller percentage of interactions did result in a successful match. This indicates that the success rate of finding a match was relatively low during the speed dating events(well 4 minutes seems not enough to like someone !!). so there is NO love at first sight!!!!😱
## Gender differences in finding their Matches
Do women receive more positive final decisions from the other person (dec_o) than men do?
```python
# Set color palette for pink and blue
colors = {'Female': 'hotpink', 'Male': 'darkblue'}

# Create an interactive histogram with pink and blue colors
fig = px.histogram(data, x='dec_o', color='gender', color_discrete_map=colors, barmode='group')

# Update the layout of the histogram
fig.update_layout(
    title='Decision of Opposite Gender by Gender',
    xaxis_title='Decision (1: Yes, 0: No)',
    yaxis_title='Count',
    showlegend=True,  # Show the custom legend with pink and blue colors
    legend_title='Gender',  # Set the title for the legend
)

# Show the interactive histogram
fig.show()

```
![Matches by gender](decisionbygender.png)

It looks like women received about **2189** **no** and about **1986** **yes** for the decision question "Would you like to see him or her again?". Men received about **2665** **no** and about **1529** **yes**. In other words, men are more likely to be rejected by women than women are to be rejected by men. This is a statistically significant difference as confirmed by the above chi-squared test p-value. **Poor guys**!🙈
 
## Heartbreak in the Air: Unrequited Love

Speaking of gender dynamics, it seems like men had a tougher time winning hearts. The fairer sex, our charming ladies, were more likely to receive positive decisions from their dates. Go ladies! But fret not, gentlemen. Love's path is never smooth, but it's worth every pursuit.
So how many interactions were unrequited love? That is, getting the count of rows where dec_o = 1 AND dec = 0 OR a dec = 1 AND dec_o = 0?
```python
# unrequited love count
no_love_count = len(data[(data['dec_o']==0) & (data['dec']==1)]) 
+ len(data[(data['dec_o']==1) & (data['dec']==0)])
perc_broken_heart = no_love_count / len(data.index)
perc_broken_heart*100
```
So it seems **26%** of participants, unfortunately, had their **heart broken**. More than the percentage of people who got a second date!
## Out of curiosity Does attractiveness can save you from a broken heart in the speed dating world!!!
```python
# looking at url by attractiveness
plt.figure(figsize=(7,9))
sns.boxplot(x='url', y='attr', data=data, palette='cool')
plt.title('Broken Hearts by Attractiveness of Partner', fontsize=20)
plt.xlabel('Broken Heart', fontsize=16)
```

![I and My friends]({{site.baseurl}}/assets/img/brokenheart.png)
```python
import statsmodels.api as sm
# chi-square test
bh_crosstab = pd.crosstab(index=data.attr, columns=data.url)
bh_table = sm.stats.Table(bh_crosstab)
bh_rslt = bh_table.test_nominal_association()
bh_rslt.pvalue
>> 0.12624323316870334
```
## The Impact of Self-Perception: A Wondrous Tale

As the curtain lifts on the stage of self-perception, we find some intriguing revelations. The participants' own self-esteem and the way their dates perceived them often danced to different tunes. Who knew perceptions could be so versatile?

## Factors Influencing Second Date Decision: The Heart's Ultimate Goal

Let's get to the nitty-gritty of factors impacting the second date decision – the heart's ultimate goal. Clubbing and movies take the crown, with impressive importances of 0.22 and 0.20, respectively. It seems like the life of the party can steal the show on speed dates!

```python
# Calculate the probability of getting a second date for each level of the self-attributes
prob_by_self_attr = data.groupby(self_attributes)['dec_o'].mean().reset_index()

# Create an interactive bar plot for self-attributes
fig_self = px.bar(prob_by_self_attr, x=self_attributes, y='dec_o', text='dec_o', color='dec_o',
                  color_continuous_scale='Blues', range_color=[0, 1])

# Update the layout for self-attributes plot
fig_self.update_layout(title='Impact of Self-Perceived Attributes on Second Date Decision',
                       xaxis_title='Self-Perceived Attributes',
                       yaxis_title='Probability of Getting a Second Date',
                       xaxis_tickangle=-45)

# Show the plots
fig_self.show()
```

## Embrace Your Quirks: Gaming and Passionate Pursuits

* Gaming enthusiasts, don't lose hope.
* It's not about the hobby; it's about finding someone who embraces your quirks and passions.

## Compatibility Unleashed: The Impact of Shared Interests

* Approximately 26% of participants unfortunately had their hearts broken.
* Remember, love may not always be mutual, but it's the courage to try that counts!

## Career Choices: The Path to the Heart

* Outgoing souls, rejoice!
* Your adventurous spirit can seal the deal for that second date.

## Self-Perceived Attributes: Embrace the Real You!

* What you think of yourself and how others see you can be wonderfully unpredictable.
* Love's charm transcends self-doubt, and finding someone who sees the real you is the ultimate win!

## The Power of Data Science in Love

* A logistic regression model whispered the tales of impact.
* The statistics confirmed that clubbing, movies, museums, and yoga held sway over the dating dance floor.
* The journey of self-discovery, career choices, and shared interests can make love's melody sweeter.

## Conclusion

* The journey of finding love is a wild ride, filled with laughter, heartbreak, and unexpected twists.
* Embrace the adventure, for love knows no bounds, and data science adds a dash of magic to the quest for a connection.

As a data scientist, I revel in the joy of decoding the mysteries of love with you. So, folks, may your hearts find their perfect match in this chaotic, hilarious, and enchanting journey called love! ❤️ Happy dating, adventurers! 🚀

