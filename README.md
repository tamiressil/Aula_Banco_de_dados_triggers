{% extends 'base.html' %}
{% load static %}
{% block title %}Detalhes da Cidade{% endblock %}
{% block content %}
{% for item in items %}
    
    <h3>{{ item.city }}</h3>
    
    <div class="info-container">
        <p><strong>"city":</strong> {{ item.city }}</p>
        
        <p><strong>"cluster":</strong></p>
        {% for key, value in item.cluster.items %}
            <p class="indent"><strong>"{{ key }}":</strong></p>
            <p class="indent-more">
            [
                {% for coord in value %}
                    '{{ coord }}'{% if not forloop.last %},<br>{% endif %}
                {% endfor %}
            ]
            </p>
        {% endfor %}
        
        <p><strong>"demarcation":</strong></p>
        {% for key, value in item.demarcation.items %}
            <p class="indent"><strong>"{{ key }}":</strong></p>
            <p class="indent-more">
            [
                {% for coord in value %}
                    '{{ coord }}'{% if not forloop.last %},<br>{% endif %}
                {% endfor %}
            ]
            </p>
        {% endfor %}
        
        <p><strong>"federative Unit":</strong> "{{ item.federative_Unit }}"</p>
        
        <p><strong>"nacional":</strong></p>
        {% for key, value in item.nacional.items %}
            <p class="indent"><strong>"{{ key }}":</strong></p>
            {% if value|length > 0 %}
                <p class="indent-more">
                [
                    {% for channel in value %}
                        '{{ channel }}'{% if not forloop.last %},<br>{% endif %}
                    {% endfor %}
                ]
                </p>
            {% else %}
                <p class="indent-more">[]</p>
            {% endif %}
        {% endfor %}
        
        <p><strong>"nlc":</strong> "{{ item.nlc }}"</p>
    </div>
    
    <hr>
{% endfor %}

<style>
    .info-container {
        padding: 10px;
        background-color: #f5f5f5;
        border-radius: 5px;
    }
    .indent {
        margin-left: 20px;
    }
    .indent-more {
        margin-left: 40px;
        font-family: monospace;
    }
</style>
{% endblock %}
